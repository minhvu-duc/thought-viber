---
name: harden
description: Build a thought-viber feature as durable, human-owned code — a proven beta after verdict decides keep, or a direct feature after its brief is confirmed. Splits the feature into small intents, tracks them, says which intent and stage come next, and closes out once every intent has merged. Each intent is defined with harden-define and delivered with harden-deliver.
---

# harden

The feature deserves to exist: a beta proved it, or it's a direct feature that will be used regardless. Now build it so a human owns it: every decision written down, every file explainable. AI drafts and does the work; humans approve at gates. The agent never approves its own work.

The loop follows Anthropic's AI-Native SDLC Playbook: each stage commits an artifact that the next stage reads, and the commit history records who asked for what, what the agent produced, and who approved it.

**Input:**
- **Beta:** `verdict.md` (decision: keep) and the evidence pack. The beta code is reference material, not a starting point.
- **Direct** (from `direct`): `brief.md`, confirmed. There is no beta, evidence pack or flag.

**Output:** durable code, the beta deleted if there was one, and an artifact trail per intent.

Three skills share the work:

| Skill | Stages | Artifacts | Approvers |
|---|---|---|---|
| `harden` | Decompose, track, close | `index.md` | Product owner |
| `harden-define` | Intent, spec | `intent.md`, `spec.md` | Product owner, policy owners |
| `harden-deliver` | Build, test, review | code, PR | Code owner |

```
<artifacts_root>/<feature>/harden/
  index.md                     # intents, order, dependencies, status
  01-<slug>/intent.md spec.md
  02-<slug>/...
```

## 1. Start

If `harden/index.md` already exists, skip to **3. Track**.

Otherwise:

- **Beta:** set probe status to `hardening`, `hardening_since` to today and `hardening_due` to today + `max_hardening_days`.
- **Direct:** check brief.md has `confirmed` set (if not, send the user back to `direct`), then set its status to `hardening`. There's no hardening clock: the clock exists because beta code waits in the repo until hardening ends, and a direct feature has none.

## 2. Decompose into intents

### Beta

Read the evidence pack and the beta code, then propose intents:

- **One intent = one user-visible outcome**, shippable on its own, owning specific acceptance tests from `behavior-spec.md`.
- **Only what was used.** Behaviors marked `drop` get no intent; they die with the beta.
- **Data and contract first.** Intent 01 is usually the durable data model, the migration from the `beta_<feature>_*` namespace (tables and any namespaced columns on core tables), and the public interface. Other intents depend on it.
- **One source of truth for data.** From the moment the data intent merges, users' data lives in exactly one place. That intent's change set also switches the beta code still running to read and write through the new durable interface, so slices not yet hardened see the same data as hardened ones. After the cutover the `beta_<feature>_*` tables and columns are no longer written; they stay, read-only, until close-out. If the remaining beta code can't be switched in that change, the data intent's spec must say how both stores stay consistent (for example, writing to both) until the last slice lands.
- **Floor touches become durable.** Each touch listed in probe.md belongs to an intent, whose spec flags the matching policy owner. The durable version replaces a code touch; nothing from the beta stays in floor code.
- **Small enough for one spec and one reviewable PR.** If not, split again.
- **Last intent closes out:** removes the flag, the Beta label and anything left in `<beta_root>/<feature>/`.

Write `harden/index.md` from `templates/index.md`. Every acceptance test not marked `drop` belongs to exactly one intent.

### Direct

Read brief.md and the codebase, then propose intents by the same rules, with these differences:

- **Only what the brief lists**, in place of "only what was used": every behavior under Behaviors belongs to exactly one intent, and no intent does anything the brief doesn't list. Intents own behaviors, not existing acceptance tests; each intent's spec turns its behaviors into acceptance tests.
- **Data and contract first**, with no beta data to migrate and no cutover. If the feature changes existing core schema, the data intent's spec covers that change.
- **No close-out intent:** there is no flag, label or beta folder to remove.
- **Fill in the brief's "Built by intent" columns** under Interfaces for betas and Safety floor, so each path has an intent that registers it in config.

Write `harden/index.md` from `templates/index.md`, leaving out "Dropped from the beta" and the close-out row.

### Approval

**Gate (beta or direct):** the product owner (usually the sponsor) approves the decomposition.

## 3. Track

Read `index.md` and each intent folder, update every intent's status from its artifacts, and say what comes next:

| Intent status | Next |
|---|---|
| `todo`, `intent`, `spec` | `harden-define` for that intent |
| `build`, `test`, `review` | `harden-deliver` for that intent |
| `done` | nothing |

Run intents in dependency order. Intents with no dependency between them and no shared files can run in parallel, each in its own branch (and worktree, if the team uses them). Start with 2–3 in parallel and add more only while reviewers keep up.

For a beta, if today is past `hardening_due`, say so and name the sponsor.

## 4. Close

When every intent is `done`, confirm:

**Beta:**

- nothing left in `<beta_root>/<feature>/`, no `thought-viber:beta:<feature>` markers (including the acceptance-test registration), no `beta.<feature>` flag;
- no floor touch from the beta left: each was replaced by durable code;
- beta data migrated, nothing has written to the `beta_<feature>_*` tables and columns since the cutover, and they are dropped only after the owner confirms the migration;
- every incident in `feedback.md` has a regression test or eval;
- probe status set to `durable`.

Report: intents merged, what was dropped from the beta and why, and who owns each part.

**Direct:**

- every behavior in brief.md has a locked acceptance test in the durable suite;
- every interface under "Interfaces for betas" sits at a path that follows `public_interface_convention`, and that path is in config's `public_interfaces`;
- every path under "Safety floor" is in config's `floor_paths`;
- the boundary check runs in CI;
- brief status set to `durable`.

Report: intents merged, any changes recorded after confirmation, and who owns each part.
