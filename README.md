# thought-viber

Agent skills for shipping features at vibe-coding speed without giving up the ownership of a real SDLC.

## The idea

Two code tiers:

- **Beta (discovery) code** proves demand fast. It is vibe-coded, nobody reads it, it ships behind a flag with telemetry, and it is built to be thrown away.
- **Durable code** is built only where demand is proven. It goes through a careful, AI-native SDLC and a human owns every decision in it.

The beta is a probe, not a prototype: it is optimized for time-to-signal. If usage proves the feature, it is hardened slice by slice using the evidence the beta left behind (black-box acceptance tests, usage map, data shape), and the beta is removed. Whether each slice is rebuilt from the evidence or adopts beta code is the team's choice; either way it goes through the same human gates. If the feature isn't proven, it is reaped.

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
| `launch` | Checks the gate is current, turns the flag on for the probe's exposure, confirms telemetry arrives, starts the clock | Sponsor confirms exposure |
| `verdict` | Compares usage to the pre-set threshold; recommends keep / kill / pivot / extend; on keep builds the evidence pack | Sponsor decides |
| `harden` | Splits the feature into small intents; runs each through intent → spec → plan → build → test → review; deletes the beta slice by slice | Product owner, policy owners, engineer, code owner |
| `reap` | Deletes killed/expired betas (code, wiring, flag, data) and records the lesson | Sponsor confirms data handling |
| `status` | Read-only overview: every beta's status, clock, gate result and next step | — |

`harden` follows the loop from Anthropic's [AI-Native SDLC Playbook](https://claude.com/resources/articles/the-ai-native-sdlc-playbook): each stage commits an artifact the next stage reads, and the agent never approves its own work.

## Boundary rules

These are what make deletion cheap. `vibe` sets up a mechanical check for the first two; `gate` enforces all of them.

1. Durable code never imports beta code.
2. Beta code uses durable code only through declared public interfaces. When one is missing, or the feature changes existing behavior, a small durable change adds a facade or an extension point (default: today's behavior) through normal review first.
3. Beta code lives in `beta/<feature>/`; wiring outside it is minimal and marked `thought-viber:beta:<feature>`.
4. Beta data lives in its own namespace (`beta_<feature>_*`); no core schema changes.
5. Everything is behind flag `beta.<feature>`, default off, and turning it off removes the feature.
6. Betas never touch the safety floor: auth, payments, permissions, PII handling, secrets, core migrations.
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
      acceptance/        # black-box tests; outlive the beta code (path set by acceptance_dir)
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

- **Fresh-context review** (`gate`, `harden`): a subagent if the agent has them, otherwise a new session given only the listed inputs.
- **Read-only planning** (`harden`): the agent's plan mode if it has one, otherwise plan without editing.
- **Locked tests** (`harden`): a hook or file-protection rule if the agent supports one.
- **Agent instructions**: whichever file the agent reads (`AGENTS.md`, `CLAUDE.md`, `GEMINI.md`).

## License

[MIT](LICENSE)
