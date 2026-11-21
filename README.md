# thought-viber

Agent skills for shipping features at vibe-coding speed without giving up the ownership of a real SDLC.

## The idea

Two code tiers:

- **Beta (discovery) code** proves demand fast. It is vibe-coded, nobody reads it, it ships behind a flag with telemetry, and it is built to be thrown away.
- **Durable code** is built only where demand is proven. It goes through a careful, AI-native SDLC and a human owns every decision in it.

The beta is a probe, not a prototype: it is optimized for time-to-signal. If usage proves the feature, it is rebuilt from the evidence the beta left behind (black-box acceptance tests, usage map, data shape), slice by slice, and the beta code is deleted. If not, it is reaped.

Both tiers use AI. They differ in **ownership and rigor**, not in who types.

## Lifecycle

```
probe ──► vibe ──► gate ──► launch ──► (users try it) ──► verdict
                                                          │
                     ┌──────────────┬──────────────┬──────┴──────┐
                     ▼              ▼              ▼             ▼
                   keep           kill           pivot        extend
                     │              │              │          (once)
                     ▼              ▼              ▼
                  harden          reap      new probe + reap
```

Probe status: `proposed → building → live → decided → hardening → durable` or `→ reaped`.

## Skills

| Skill | What it does | Human gate |
|---|---|---|
| `probe` | Writes the hypothesis, metric, threshold, window and expiry before any code; checks the safety floor and the live-beta cap | Sponsor confirms probe.md |
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

The skills follow the open [Agent Skills](https://agentskills.io) format (`skills/<name>/SKILL.md`), so any agent that reads skills can use them. Install separately for each agent you use.

### Claude Code

```
/plugin marketplace add minhvu-duc/thought-viber
/plugin install thought-viber@thought-viber
```

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

The repo ships `.codex-plugin/` and `.cursor-plugin/` manifests for plugin marketplaces. Without a marketplace, copy `skills/*` into the folder your agent reads skills from:

| Agent | Project skills folder |
|---|---|
| Claude Code | `.claude/skills/` |
| Codex | `.agents/skills/` |
| Cursor | `.cursor/skills/` |
| OpenCode | `.opencode/skills/` |
| GitHub Copilot | `.github/skills/` |

### Agent differences

The skills don't depend on one agent's tools. Where a step benefits from an agent feature, it says what to do without it:

- **Fresh-context review** (`gate`, `harden-deliver`): a subagent if the agent has them, otherwise a new session given only the listed inputs.
- **Read-only planning** (`harden-deliver`): the agent's plan mode if it has one, otherwise plan without editing.
- **Locked tests** (`harden-deliver`): a hook or file-protection rule if the agent supports one.
- **Agent instructions**: whichever file the agent reads (`AGENTS.md`, `CLAUDE.md`, `GEMINI.md`).

## License

[MIT](LICENSE)
