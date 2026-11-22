---
name: design
description: Write the spec for one piece of a thought-viber plan — behaviors, interfaces, data, errors and acceptance tests. Use when the plan's next piece is at todo or design. Spec only, with no implementation plan or task list.
---

# design

One spec per piece, at `.thought-viber/features/<feature>/NN-<slug>.md`, from `templates/spec.md`. It says what the code must do, precisely enough to test. It doesn't say how to build it step by step; `build` works that out.

1. **Read** the plan's row for this piece and the code it will touch. For a transitioned beta, also read the beta code and black-box tests this piece replaces.
2. **Write the spec:**
   - behaviors, each as trigger → result, with the acceptance test(s) that prove it;
   - interfaces and contracts;
   - data, and the migration if this piece has one;
   - errors and edge cases;
   - non-functional needs (security, performance, accessibility) where they matter.
3. **Don't guess.** Anything the user has to decide, such as a security or privacy question or a UX call, goes under Open questions.
4. **Check for beta code.** If the design would use beta code, follow the beta-code rule in the project's agent instructions.
5. **Approve.** The user approves the spec and answers the open questions. Set the piece to `build` in the plan.

If the spec shows the piece is too big for one PR, split it in the plan and note the change there.
