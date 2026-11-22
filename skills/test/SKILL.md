---
name: test
description: Verify one built piece of a thought-viber plan and get it merged — run every test, check the work against its spec with fresh eyes, and open the PR. Use when the plan's next piece is at test. On the last piece, confirms the whole feature is done.
---

# test

1. **Run everything.** The piece's acceptance tests, the full suite and the build, all green. For UI, compare screenshots against the spec.
2. **Fresh eyes.** In a context that hasn't seen the build (a subagent if your agent has them, otherwise a new session given only the spec and the diff), check:
   - it does what the spec says, and only that;
   - bugs and missed edge cases;
   - security: auth on every entry point, input handling, data exposure;
   - no use of beta code.

   The reviewer reports; it doesn't fix. Fix what it finds and rerun.
3. **Transitioned beta:** nothing in the beta folder or in marked lines still handles this piece's behavior.
4. **Open the PR**, linking the plan and the spec. It goes through the team's normal review, and whoever approves it should be able to explain every changed file.
5. **After merge,** set the piece to `done` in the plan and point to the next one (`design`).

## Last piece

Confirm the plan's outcome holds and every in-scope behavior has an acceptance test. For a transitioned beta, also confirm there's no beta folder, no `thought-viber:beta:<feature>` marker and no flag left, the old beta data was dropped once the owner confirmed the move, and set the beta file's status to `permanent`.
