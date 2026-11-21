# thought-viber

Agent skills for shipping features at vibe-coding speed without giving up the ownership of a real SDLC.

## The idea

Two code tiers:

- **Beta (discovery) code** proves demand fast. It is vibe-coded, nobody reads it, it ships behind a flag with telemetry, and it is built to be thrown away.
- **Durable code** is built only where demand is proven. It goes through a careful, AI-native SDLC and a human owns every decision in it.

The beta is a probe, not a prototype: it is optimized for time-to-signal. If usage proves the feature, it is rebuilt from the evidence the beta left behind (black-box acceptance tests, usage map, data shape), slice by slice, and the beta code is deleted. If not, it is reaped.

Both tiers use AI. They differ in **ownership and rigor**, not in who types.

## Workflow

```
probe ─► vibe ─► gate ─► deploy ────────► launch ───────► trial ─► verdict
          ▲       │      (feature off)    (feature on)      ▲         │
          └─FAIL──┘                                         └─ extend ┤
                                                                      ├─ kill  ─► reap
                                                                      ├─ pivot ─► new probe + reap
                                                                      └─ keep  ─► harden
                                                                                    │
                                                                                    ├─► per intent: harden-define ─► harden-deliver
                                                                                    └─► close-out ─► durable
```

Every beta sits behind a **feature flag**: an on/off switch the app checks before showing the feature.

- **feature off:** the code is deployed to production with its flag off, so no user can see the feature yet.
- **feature on:** `launch` switches the flag on for the users the probe chose. The trial starts.
- **extend:** the trial ended before enough users tried the feature to judge it, so it gets more time and `verdict` runs again later.

Probe status: `proposed → building → live → decided → hardening → durable`, or `decided → reaped`. Run `status` at any point to see every beta and what to do next.

### 1. `probe` — decide what you're testing

*Status: `proposed` → `building`*

- On first run, creates `.thought-viber/config.yaml` with you: beta folder, public interfaces, safety floor, flag system, telemetry source.
- Checks there's room under `max_live_betas`, shows any similar lesson in `LEARNINGS.md`, and checks the feature stays off the safety floor. If it can't, it offers a narrower version, or building it as durable code directly with `harden-define` and `harden-deliver`.
- Pins down, with you: who sees it, the hypothesis, one primary metric and its events, the keep threshold, kill-early criteria, pivot signals, the minimum sample, how many days the trial runs, and the sponsor.
- Lists any durable prep the beta needs: a facade over logic with no public interface, or an extension point where the feature changes existing behavior.

**You get** `probe.md`. **The sponsor confirms it**, and from then on its thresholds can only change through a recorded amendment.

### 2. `vibe` — build the beta fast

*Status: `building`*

- Lands any durable prep first, each as its own normally reviewed PR.
- Adds a boundary check to CI if the repo doesn't have one.
- Builds the smallest version that answers the question, in `beta/<feature>/`, behind flag `beta.<feature>` (default off), with a Beta label, the probe's telemetry events and data in the `beta_<feature>_*` namespace.
- Writes acceptance tests that use the feature from outside, registers them with the test runner, and proves that switching the flag off hides the feature and that every event fires.

**You get** beta code, acceptance tests and `build-notes.md`. Nobody reviews the code; `gate` checks the boundary around it.

### 3. `gate` — check it's safe to release

*Status: `building`*

- Deterministic checks: imports respect the boundary, outside changes are only marked wiring, safety floor and existing schema untouched, no secrets, dependencies audited, flag defaults off, events fire, all tests pass.
- An independent review in a fresh context: authorization, input handling, data exposure, resource use, failure isolation.

**You get** `gate.md` with PASS or FAIL. On FAIL, fixes go back through `vibe` and `gate` runs again. A human may accept a medium or low finding; any critical or high one is a FAIL.

### 4. Deploy — your normal pipeline

Merge and deploy as usual, with the feature's flag off. The beta is in production, but no user can see it yet. This isn't a skill.

### 5. `launch` — turn it on for users

*Status: `building` → `live`*

- Checks the gated commit is the one deployed, nothing changed since the gate, and there's still room under the cap.
- **The sponsor confirms who sees it.** The flag is turned on for that exposure, which is what makes the feature live: instantly with a runtime flag system, or on the next deploy if flags live in code or config.
- Smoke check: a user inside the exposure sees it, one outside doesn't, and events arrive.

**You get** `live_since` and `expires` set in `probe.md`. The trial has started.

### 6. The trial — users try it

*Status: `live`*

The trial runs from `launch` until its end date (`expires`), for the number of days set in the probe (21 by default). Usage builds up in your telemetry. Nothing in thought-viber runs during the trial, but you can turn the flag off at any time as a kill switch, and run `verdict` early if the kill-early criteria look hit.

### 7. `verdict` — keep, kill, pivot or extend

*Status: `live` → `decided`*

- Pulls the numbers from your telemetry, computes them in code, and compares them to the thresholds locked in `probe.md`.
- Recommends an outcome; **the sponsor decides**. A beta that expires with no verdict defaults to kill.
  - **extend:** the trial ended before enough users tried the feature (fewer than the probe's minimum sample), so there isn't enough data to judge. The trial gets a new end date, users keep trying it, and `verdict` runs again then. Allowed `max_extensions` times (once by default).
  - **kill:** go to `reap`.
  - **pivot:** a new `probe` linked to this one, then `reap` this beta.
  - **keep:** builds the evidence pack (behavior spec, usage map, data shape, feedback), then go to `harden`.

**You get** `verdict.md` with the numbers, the recommendation and the sponsor's decision.

### 8. `harden` — plan the rebuild

*Status: `decided` → `hardening`*

- Starts the hardening clock (`max_hardening_days`).
- Splits the feature into intents: one user-visible outcome each, only behaviors people used, small enough for one PR. Data and contract come first; the last intent closes out.

**You get** `harden/index.md`; **the product owner approves the split**. Run `harden` again at any point to see which intent and stage is next.

### 9. `harden-define` — define one intent

- Writes `intent.md`: problem, outcome, constraints, the acceptance tests it owns. **The product owner accepts it.**
- Writes `spec.md`: behaviors, interfaces, data, errors, non-functional needs, with policy concerns flagged. For the data intent, it includes the cutover to a single data store. **The product owner and policy owners resolve every flag.**

### 10. `harden-deliver` — deliver one intent

- **Plan:** `plan.md`, written without editing code. **An engineer approves it.**
- **Build:** the intent's acceptance tests go into the durable suite first and are locked. Then the implementation, and in the same change, this slice is rerouted from beta to durable and its beta code deleted. The data intent also migrates the data and switches the remaining beta code to the durable store, so users' data lives in one place.
- **Test:** the plan's "done" commands, the full suite and the boundary check, then an independent verifier.
- **Review:** a PR checked against `REVIEW.md`. **A code owner approves** and can explain every changed file.

Users keep using the feature throughout: each slice swaps beta code for durable code behind the same flag, and the locked tests keep its behavior the same.

### 11. `harden` close-out — durable

*Status: `hardening` → `durable`*

When every intent has merged, `harden` confirms there's no beta folder, wiring marker or flag left, the beta data was migrated and its tables dropped once the owner confirmed, and every incident from the trial has a regression test.

### 12. `reap` — delete a beta that didn't make it

*Status: `decided` → `reaped`*

- Confirms the flag is off. **The sponsor decides what happens to the beta's data:** export it, hand it to a pivot probe, or drop it.
- Deletes the beta folder, wiring, flag, events, beta-only dependencies and namespaced data. Acceptance tests are kept as records but no longer run. Durable prep stays unless its owner removes it.
- Build and tests must stay green, then one PR: `reap: <feature>`.

**You get** the lesson recorded in `verdict.md` and one line in `LEARNINGS.md`, so the next `probe` sees it.

## Skills

| Skill | What it does | Human gate |
|---|---|---|
| `probe` | Writes the hypothesis, metric, threshold, trial length and end date before any code; checks the safety floor and the live-beta cap | Sponsor confirms probe.md |
| `vibe` | Builds the beta fast inside the boundary, behind a flag, with telemetry and black-box acceptance tests | — |
| `gate` | Deterministic boundary/safety checks + independent AI review; pass/fail report | Accepting any medium/low finding |
| `launch` | Turns the flag on for the probe's exposure, which makes the already-deployed code live for those users; confirms telemetry arrives and starts the clock | Sponsor confirms exposure |
| `verdict` | Compares usage to the pre-set threshold; recommends keep / kill / pivot / extend; on keep builds the evidence pack | Sponsor decides |
| `harden` | Splits the kept feature into small intents, tracks them, says which intent and stage is next, and closes out when all have merged | Product owner approves the decomposition |
| `harden-define` | For one intent: writes `intent.md`, then `spec.md` with policy concerns flagged | Product owner; policy owners resolve flags |
| `harden-deliver` | For one intent: writes `plan.md`, builds against locked acceptance tests, deletes the beta slice it replaces, verifies, opens the PR | Engineer approves the plan; code owner approves the PR |
| `reap` | Deletes killed/expired betas (code, wiring, flag, data) and records the lesson | Sponsor confirms data handling |
| `status` | Read-only overview: every beta's status, clock, gate result and next step | — |

`harden`, `harden-define` and `harden-deliver` follow the loop from Anthropic's [AI-Native SDLC Playbook](https://claude.com/resources/articles/the-ai-native-sdlc-playbook): each stage commits an artifact the next stage reads, and the agent never approves its own work.

## Roles

One person can hold several roles; on a small team one person may hold them all.

| Role | Who it usually is | What they decide |
|---|---|---|
| **Sponsor** | The PM, founder or lead who wants the feature | Owns the bet, not the code: confirms the probe and its thresholds, approves who sees the beta at launch, makes the keep / kill / pivot / extend call, decides what happens to beta data |
| **Product owner** | Usually the sponsor | What the durable feature does: approves the harden decomposition, each intent and each spec |
| **Policy owners** | Security, compliance, UX or brand owners | Resolve the concerns a spec flags in their area |
| **Engineer** | The engineer building the slice | Approves each `plan.md` |
| **Code owner** | Whoever owns that part of the codebase | Approves each harden PR and can explain every changed file |

## Boundary rules

These are what make deletion cheap. `vibe` sets up a mechanical check for the first two; `gate` enforces all of them.

1. Durable code never imports beta code.
2. Beta code uses durable code only through declared public interfaces. When one is missing, or the feature changes existing behavior, a small durable change adds a facade or an extension point (default: today's behavior) through normal review first.
3. Beta code lives in `beta/<feature>/`; wiring outside it is minimal and marked `thought-viber:beta:<feature>`.
4. Beta data lives in its own namespace: `beta_<feature>_*` tables, collections or keys. If it must sit on existing core data, it goes in new namespaced columns (`beta_<feature>_*`); existing columns are never changed. If neither works, stop and discuss with the team.
5. Everything is behind flag `beta.<feature>`, default off. The code ships through a normal deploy with the flag off; turning the flag on (`launch`) is what makes the feature live, and turning it off is the kill switch that hides it from every user. With a runtime flag system (a flag service or flags table) both switches are instant; with flags in code or config, each one takes effect on the next deploy. Each project chooses (`flag_system` in config). The code stays until `reap` deletes it (or `harden` replaces it).
6. Betas never touch the safety floor. The defaults are auth, payments, permissions, PII handling, secrets and existing core schema; each project adjusts the list in `config.yaml` (`safety_floor`, plus `floor_paths` for the code that implements it).
7. At most `max_live_betas` building, live or hardening at once; every beta has an expiry, and the default outcome is deletion.

## Layout in your project

```
.thought-viber/
  config.yaml
  LEARNINGS.md
  features/<feature>/
    probe.md
    build-notes.md
    gate.md
    verdict.md
    evidence/
      acceptance/        # tests that use the feature from outside (HTTP, UI, CLI); kept
                         # after beta code is deleted, they are the spec harden rebuilds against
      behavior-spec.md
      usage-map.md
      data-shape.md
      feedback.md
    harden/
      index.md
      01-<slug>/intent.md spec.md plan.md
beta/<feature>/          # beta code
```

## Install

Every method below installs straight from this GitHub repo; there's no store or marketplace to sign up for. The skills follow the open [Agent Skills](https://agentskills.io) format (`skills/<name>/SKILL.md`), so any agent that reads skills can use them. Install separately for each agent you use.

### Claude Code

```
/plugin marketplace add minhvu-duc/thought-viber
/plugin install thought-viber@thought-viber
```

The "marketplace" here is this repo itself: `.claude-plugin/marketplace.json` lets Claude Code install it straight from GitHub.

### Gemini CLI

```bash
gemini extensions install https://github.com/minhvu-duc/thought-viber
```

### Factory Droid

```bash
droid plugin marketplace add https://github.com/minhvu-duc/thought-viber
```

```bash
droid plugin install thought-viber@thought-viber
```

### Codex, Cursor, OpenCode, GitHub Copilot and others

Copy `skills/*` into the folder your agent reads skills from:

| Agent | Project skills folder |
|---|---|
| Claude Code | `.claude/skills/` |
| Codex | `.agents/skills/` |
| Cursor | `.cursor/skills/` |
| OpenCode | `.opencode/skills/` |
| GitHub Copilot | `.github/skills/` |

The `.codex-plugin/` and `.cursor-plugin/` folders are only for listing this repo in the official Codex or Cursor plugin marketplaces later. You don't need them to install.

### Agent differences

The skills don't depend on one agent's tools. Where a step benefits from an agent feature, it says what to do without it:

- **Fresh-context review** (`gate`, `harden-deliver`): a subagent if the agent has them, otherwise a new session given only the listed inputs.
- **Read-only planning** (`harden-deliver`): the agent's plan mode if it has one, otherwise plan without editing.
- **Locked tests** (`harden-deliver`): a hook or file-protection rule if the agent supports one.
- **Agent instructions**: whichever file the agent reads (`AGENTS.md`, `CLAUDE.md`, `GEMINI.md`).

## License

[MIT](LICENSE)
