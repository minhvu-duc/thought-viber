---
name: harden
description: Rebuild a proven thought-viber beta as durable, human-owned code. Use after verdict decides keep, to split the feature into small intents, track them, say which intent and stage come next, and close out once every intent has merged. Each intent is defined with harden-define and delivered with harden-deliver.
---

# harden

The beta proved the feature deserves to exist. Now rebuild it so a human owns it: every decision written down, every file explainable. AI drafts and does the work; humans approve at gates. The agent never approves its own work.

The loop follows Anthropic's AI-Native SDLC Playbook: each stage commits an artifact that the next stage reads, and the commit history records who asked for what, what the agent produced, and who approved it.

**Input:** `verdict.md` (decision: keep) and the evidence pack. The beta code is reference material, not a starting point.
**Output:** durable code, the beta deleted, and an artifact trail per intent.

Three skills share the work:

| Skill | Stages | Artifacts | Approvers |
|---|---|---|---|
| `harden` | Decompose, track, close | `index.md` | Product owner |
| `harden-define` | Intent, spec | `intent.md`, `spec.md` | Product owner, policy owners |
| `harden-deliver` | Plan, build, test, review | `plan.md`, code, PR | Engineer, code owner |

```
<artifacts_root>/<feature>/harden/
  index.md                     # intents, order, dependencies, status
  01-<slug>/intent.md spec.md plan.md
  02-<slug>/...
```

## 1. Start

If `harden/index.md` already exists, skip to **3. Track**.

Otherwise set probe status to `hardening`, `hardening_since` to today and `hardening_due` to today + `max_hardening_days`.

## 2. Decompose into intents

Read the evidence pack and the beta code, then propose intents:

- **One intent = one user-visible outcome**, shippable on its own, owning specific acceptance tests from `behavior-spec.md`.
- **Only what was used.** Behaviors marked `drop` get no intent; they die with the beta.
- **Data and contract first.** Intent 01 is usually the durable data model, the migration from the `beta_<feature>_*` namespace (tables and any namespaced columns on core tables), and the public interface. Other intents depend on it.
- **One source of truth for data.** From the moment the data intent merges, users' data lives in exactly one place. That intent's change set also switches the beta code still running to read and write through the new durable interface, so slices not yet hardened see the same data as hardened ones. After the cutover the `beta_<feature>_*` tables and columns are no longer written; they stay, read-only, until close-out. If the remaining beta code can't be switched in that change, the data intent's spec must say how both stores stay consistent (for example, writing to both) until the last slice lands.
- **Small enough for one `plan.md` and one reviewable PR.** If not, split again.
- **Last intent closes out:** removes the flag, the Beta label and anything left in `<beta_root>/<feature>/`.

Write `harden/index.md` from `templates/index.md`. Every acceptance test not marked `drop` belongs to exactly one intent.

**Gate:** the product owner (usually the sponsor) approves the decomposition.

## 3. Track

Read `index.md` and each intent folder, update every intent's status from its artifacts, and say what comes next:

| Intent status | Next |
|---|---|
| `todo`, `intent`, `spec` | `harden-define` for that intent |
| `plan`, `build`, `test`, `review` | `harden-deliver` for that intent |
| `done` | nothing |

Run intents in dependency order. Intents with no dependency between them and no shared files can run in parallel, each in its own branch (and worktree, if the team uses them). Start with 2–3 in parallel and add more only while reviewers keep up.

If today is past `hardening_due`, say so and name the sponsor.

## 4. Close

When every intent is `done`, confirm:

- nothing left in `<beta_root>/<feature>/`, no `thought-viber:beta:<feature>` markers (including the acceptance-test registration), no `beta.<feature>` flag;
- beta data migrated, nothing has written to the `beta_<feature>_*` tables and columns since the cutover, and they are dropped only after the owner confirms the migration;
- every incident in `feedback.md` has a regression test or eval;
- probe status set to `durable`.

Report: intents merged, what was dropped from the beta and why, and who owns each part.
