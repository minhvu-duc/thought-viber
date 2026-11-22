---
name: test
description: Verify one built intent of a thought-viber plan and get it merged — run every test, check the work against its spec with fresh eyes, and open the PR. Use when the plan's next intent is at test. On the last intent, confirms the whole feature is done.
---

# test

1. **Run everything.** The intent's acceptance tests, the full suite and the build, all green. For UI, compare screenshots against the spec.
2. **Transitioned beta:**
   - **Build intents:** every kept black-box test this intent owns is turned on and passes unchanged, or its change is recorded with the owner's OK. That's what shows users get the same feature.
   - **Intent 00:** no beta folder, no `thought-viber:beta:<feature>` marker and no flag left; the beta's data is still there; the kept tests are skipped, not deleted.
3. **Fresh eyes.** In a context that hasn't seen the build (a subagent if your agent has them, otherwise a new session given only the spec and the diff), check:
   - it does what the spec says, and only that;
   - bugs and missed edge cases;
   - security: auth on every entry point, input handling, data exposure;
   - no use of beta code.

   The reviewer reports; it doesn't fix. Fix what it finds and rerun.
4. **Open the PR**, linking the plan and the spec. It goes through the team's normal review, and whoever approves it should be able to explain every changed file. If the plan says the rebuild is **held**, the PR targets the rebuild branch rather than the main branch.
5. **After merge,** set the intent to `done` in the plan and point to the next one (`design`).

## Last intent

Confirm the plan's outcome holds and every in-scope behavior has an acceptance test. For a transitioned beta, also confirm no kept test is still skipped, the old beta data was dropped once the owner confirmed the move, and (if held) the rebuild branch is ready to merge. Then set the beta file's status to `permanent`.
