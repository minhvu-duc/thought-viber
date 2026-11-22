---
name: transition
description: Plan how a kept thought-viber beta becomes permanent code. Use after verdict says keep; reads the beta file, its usage and its code, and writes the plan the SDLC flow follows — what to keep and drop, the pieces, how the data moves, and which beta code each piece removes.
---

# transition

The beta proved people want the feature. Its code was written to be thrown away, so the permanent version is rebuilt rather than cleaned up: piece by piece, each piece replacing and deleting the beta code it covers. Users keep the feature the whole time, behind the same flag.

`transition` is the `plan` step of the SDLC flow for a feature that started as a beta. After it, each piece goes `design` → `build` → `test`.

## 1. Gather

- The beta file: idea, footprint, watch log, verdict, "Depended on by".
- The beta code and its black-box tests. They show what the feature does; they aren't a starting point for the permanent code.
- Usage by part, from the watch log. If it isn't broken down by part, run `watch` first.

## 2. Write the plan

Write `.thought-viber/features/<feature>/plan.md` from the `plan` skill's template (`plan/templates/plan.md`), with `from: transition` and the "From the beta" section filled in:

- **Keep and drop.** List what the beta does, with each part's share of usage. Parts nobody used are dropped and die with the beta. The kept parts become the plan's in-scope behaviors.
- **Pieces.** Split the kept behaviors into pieces: one outcome each, small enough for one spec and one PR. Data first.
- **Data move.** The first piece builds the permanent data model, moves the `beta_<feature>_*` data into it, and points the beta code still running at the permanent store, so users' data lives in one place from then on.
- **Swap, don't duplicate.** For each piece, name the beta code it replaces: files in the beta folder and marked lines elsewhere. That piece's build deletes them in the same change. Beta and permanent code never both handle the same behavior.
- **Dependents.** For each entry under "Depended on by", name the piece that gives it a permanent replacement.
- **Tests.** The beta's black-box tests for kept behaviors become the pieces' starting acceptance tests.
- **Last piece:** remove the flag and the Beta label, delete whatever is left of the beta folder and its markers, and drop the old beta data once the owner confirms the move.

## 3. Approve

The owner approves the plan. Then `design` the first piece.

When the last piece is done, `test` sets the beta's status to `permanent`.
