---
name: design
description: Write the spec for one intent of a thought-viber plan — behaviors, interfaces, data, errors and acceptance tests. Use when the plan's next intent is at todo or design. Spec only, with no implementation plan or task list.
---

# design

One spec per intent, at `.thought-viber/features/<feature>/NN-<intent>.md`, from `templates/spec.md`. It says what the code must do, precisely enough to test. It doesn't say how to build it step by step; `build` works that out.

1. **Read** the plan's row for this intent and the code it will touch.
2. **Write the spec:**
   - behaviors, each as trigger → result, with the acceptance test(s) that prove it;
   - interfaces and contracts;
   - data, and the migration if this intent has one;
   - errors and edge cases;
   - non-functional needs (security, performance, accessibility) where they matter.
3. **Transitioned beta:**
   - **Build intents:** the kept black-box tests the plan assigns to this intent are its acceptance tests, as they are. Add tests only for what they don't cover. Read the beta's code for reference if it's still around; the tests, not the code, define the behavior.
   - **Intent 00 (remove-beta):** the spec lists exactly what's deleted (from Footprint and a search for `thought-viber:beta:<feature>`), what stays (the kept tests, moved and skipped; the beta's data), how each dependent works until its replacement lands, and what users see meanwhile (gap or held, from the plan). Its acceptance tests: no marker or beta folder left, and the rest of the app builds and passes.
4. **Don't guess.** Anything the user has to decide, such as a security or privacy question or a UX call, goes under Open questions.
5. **Check for beta code.** If the design would use beta code, follow the beta-code rule in the project's agent instructions.
6. **Approve.** The user approves the spec and answers the open questions. Set the intent to `build` in the plan.

If the spec shows the intent is too big for one PR, split it in the plan and note the change there.
