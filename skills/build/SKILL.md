---
name: build
description: Build one specced intent of a thought-viber plan as permanent code — acceptance tests first, then the code. For a kept beta, also covers intent 00, which removes the beta code completely. Use when the plan's next intent is at build. Warns before permanent code uses beta code.
---

# build

1. **Read the approved spec.** If there isn't one, go to `design`.
2. **Tests first.** Write the spec's acceptance tests in the permanent test suite and run them: they should fail. For a transitioned beta, turn on the kept black-box tests this intent owns, exactly as they are, and add the spec's other tests. Don't change a test to make the code pass. If a test is wrong, ask the user and record the change in the spec (and, for a kept test, in the plan).
3. **Build** what the spec says and only that, following the project's agent instructions file. If the code needs to differ from the spec, ask, and record it in the spec.
4. **No beta code.** Permanent code doesn't import, call or extend beta code. If it would, stop and follow the beta-code rule in the project's agent instructions.
5. **Data intent of a transitioned beta:** move the `beta_<feature>_*` data into the permanent store. Drop the old beta data only once the owner confirms the move.
6. Set the intent to `test` in the plan.

## Intent 00: remove the beta

For a transitioned beta, the first intent deletes rather than builds:

1. Move the kept black-box tests out of the beta folder into the permanent suite, skipped, each with a note naming the intent that turns it back on.
2. Delete everything the spec lists: the beta folder, every `thought-viber:beta:<feature>` marker and marked block (restoring edits to existing code from Footprint), the flag, the event definitions and beta-only dependencies. Leave the beta's data alone.
3. Make each dependent work without the beta, as the spec says.
4. Search again for `thought-viber:beta:<feature>` and the beta folder: nothing left. Build and full suite green, with the kept tests skipped, not deleted.
5. Set the intent to `test` in the plan.
