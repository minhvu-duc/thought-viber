---
name: plan
description: Start a thought-viber permanent feature, the first step of the SDLC flow (plan, design, build, test). Use for a feature that will be built whether or not anyone uses it — sign-in, the core schema, something a contract requires. Writes what it does, what it doesn't, and the pieces to build in order. For a kept beta, use transition instead.
---

# plan

Permanent code is code someone owns: decisions written down, every file explainable. AI does the work; a person approves the plan and each spec.

1. **Is it a bet?** Ask: *"If nobody used it, would you still build it?"* If not, it's a beta. Suggest `idea`.
2. **Pin it down with the user:**
   - **Outcome:** what's true for users when it's done.
   - **In scope:** the behaviors, each short and testable from the outside.
   - **Out of scope:** name the tempting extras. Anything not in scope is out.
   - **Constraints:** compliance, deadlines, compatibility, performance.
3. **Check for beta code.** If the feature would use code in the beta folder or marked `thought-viber:beta:*`, follow the beta-code rule in the project's agent instructions: warn the user, suggest a way around it, and record the choice in the plan.
4. **Split into pieces.** One outcome each, small enough for one spec and one PR. Data and interfaces first. Note what each depends on.
5. **Write** `.thought-viber/features/<feature>/plan.md` from `templates/plan.md` with `from: plan`, leaving out "From the beta". The user approves it. Then `design` the first piece.

After approval, scope changes go under Changes with a date and reason, not as silent edits.
