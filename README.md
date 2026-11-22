# thought-viber

Agent skills for trying features as throwaway betas, watching how much people use them, and then either reaping them or rebuilding them as permanent code.

## The idea

- **A beta is a bet.** Build it fast, put it behind a flag, show it to users, and watch how much they use it. Most bets lose, and that's fine.
- **Make it easy to reap.** A beta is only cheap if removing it is cheap. Its code goes in its own folder where it can, and anything that has to live elsewhere carries a comment saying it was written for the beta and may be removed. Reaping is: flag off, delete the folder, delete the marked lines.
- **Don't build on a beta.** Beta code may disappear. When later work wants to use it, the agent warns and suggests a way around it.
- **Permanent code goes through an SDLC.** A beta that earns its place is broken into small intents, its beta code is removed in one clean step, and the full version is built intent by intent. The beta's black-box tests make sure users get the same feature. Features that aren't a bet (sign-in, the core schema, something a contract requires) start in the SDLC directly.

## Two flows

```
Beta flow    idea ─► vibe ─► watch ─► verdict ─┬─ keep   ─► transition ─► SDLC flow
                                               ├─ extend ─► watch some more
                                               └─ kill   ─► reap

SDLC flow    plan ─► design ─► build ─► test
                     └──── per intent ─────┘
```

A kept beta's plan comes from `transition`, and its intents run in this order:

```
intent 00: remove the beta  ─►  intent 01: data  ─►  intent 02 ...  ─►  last intent
(design ─► build ─► test)       (each: design ─► build ─► test, kept black-box tests turned back on)
```

### Beta flow

| Skill | What it does |
|---|---|
| `idea` | Writes the bet down before any code: who sees it, what counts as real use, how long to watch, and what would make it worth keeping. Sets the project up on first run. |
| `vibe` | Builds the smallest version fast, behind a flag, with usage events and a few black-box tests. Keeps code in the beta folder where it can and marks the rest. Ships it with the flag off, then turns it on for the audience. |
| `watch` | Catches up on how much people use it: users, repeat use, trend, which parts get used. Logs a dated snapshot. With no feature named, shows every beta and feature and what's next. |
| `verdict` | Compares usage to the bar written at `idea` and recommends keep, kill or extend. The beta's owner decides. |
| `reap` | Deletes a killed beta: folder, marked lines, flag, events and (with the owner's yes) its data. Warns about anything that came to depend on it, and records the lesson in `LEARNINGS.md`. |
| `transition` | For a kept beta, writes the plan to make it permanent: what to keep and drop by usage, small and clear intents, which black-box tests each intent must pass unchanged, an intent 00 that removes the beta code completely, and whether users go without the feature during the rebuild or it ships all at once. |

### SDLC flow

| Skill | What it does |
|---|---|
| `plan` | Outcome, in scope, out of scope, constraints, and the small intents to build in order. The user approves it. (A kept beta gets its plan from `transition`.) |
| `design` | The spec for one intent: behaviors, interfaces, data, errors and acceptance tests. Spec only, no implementation plan. The user approves it. |
| `build` | Acceptance tests first, then the code. For a kept beta, intent 00 deletes the beta code completely and each later intent turns its kept black-box tests back on. |
| `test` | Runs everything, checks the work against the spec with fresh eyes, and opens the PR for normal review. On the last intent, confirms the feature is done and, for a kept beta, that every kept black-box test passes. |

## Beta code

Best effort, not a gate:

1. **Beta folder first.** Feature code goes in `beta/<feature>/`.
2. **Mark what can't.** Routes, menu entries, hooks into existing code and migrations that have to live elsewhere get a marker on the line above, or `begin`/`end` markers around a block:

   ```ts
   // thought-viber:beta:quick-reply — written for the quick-reply beta; may be removed. Don't build on it.
   app.route("/quick-reply", quickReply)
   ```

3. **Flag everything.** Behind `beta.<feature>`, off by default. Off means exactly today's behavior.
4. **Name the data.** `beta_<feature>_*` tables, columns and keys, so reap can find them.
5. **Warn before building on it.** On first run, `idea` adds a short rule to the project's agent instructions file (`AGENTS.md`, `CLAUDE.md`, `GEMINI.md`), so every agent in the repo knows it. When a task needs beta code, the agent stops and suggests one of: write your own version, wait for the verdict, promote that piece to permanent code now, or use it anyway with a `thought-viber:uses-beta:<feature>` marker so reap and transition deal with it.

## Layout in your project

```
.thought-viber/
  config.yaml
  LEARNINGS.md                  # one line per reaped beta
  betas/<feature>.md            # one file per beta: idea, footprint, watch log, verdict
  features/<feature>/
    plan.md                     # from plan or transition
    00-remove-beta.md           # specs from design, one per intent
    01-<intent>.md
beta/<feature>/                 # beta code
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

Copy `skills/*` into the folder your agent reads skills from. Keep them together: `transition` uses the `plan` skill's template.

| Agent | Project skills folder |
|---|---|
| Claude Code | `.claude/skills/` |
| Codex | `.agents/skills/` |
| Cursor | `.cursor/skills/` |
| OpenCode | `.opencode/skills/` |
| GitHub Copilot | `.github/skills/` |

The `.codex-plugin/` and `.cursor-plugin/` folders are only for listing this repo in the official Codex or Cursor plugin marketplaces later. You don't need them to install.

### Agent differences

The skills don't depend on one agent's tools. The fresh-eyes check in `test` uses a subagent if the agent has them, otherwise a new session given only the spec and the diff. The beta-code rule goes in whichever instructions file the agent reads.

## License

[MIT](LICENSE)
