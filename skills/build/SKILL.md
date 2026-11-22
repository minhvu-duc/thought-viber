---
name: build
description: Build one specced piece of a thought-viber plan as permanent code — acceptance tests first, then the code — and, for a kept beta, delete the beta code the piece replaces. Use when the plan's next piece is at build. Warns before permanent code uses beta code.
---

# build

1. **Read the approved spec.** If there isn't one, go to `design`.
2. **Tests first.** Write the spec's acceptance tests in the permanent test suite; for a transitioned beta, start from the beta's black-box tests. Run them: they should fail. Don't change a test to make the code pass. If a test is wrong, ask the user and record the change in the spec.
3. **Build** what the spec says and only that, following the project's agent instructions file. If the code needs to differ from the spec, ask, and record it in the spec.
4. **No beta code.** Permanent code doesn't import, call or extend beta code. If it would, stop and follow the beta-code rule in the project's agent instructions.
5. **Transitioned beta: swap in the same change.** Route this piece's behavior to the new code and delete the beta code the plan says it removes: its files in the beta folder and its marked lines elsewhere. For the data piece, move the data and point the remaining beta code at the permanent store, as the plan says.
6. Set the piece to `test` in the plan.
