---
name: direct
description: Start a thought-viber feature as durable code with no beta. Use when the feature will be used whether or not anyone tests demand first — foundations like auth, users or the core schema, obligations like a contract or compliance requirement, or safety-floor work — so a probe and trial would only confirm what is already known. Writes brief.md and hands off to harden.
---

# direct

A beta answers one question: does this feature deserve to exist? Some features don't raise that question. Every user goes through them or depends on them (sign-in, the user model, the core schema), or something outside the product requires them (a contract, a regulation). For these, a probe and a trial would only confirm what is already known. `direct` skips the beta and goes straight to the durable build: the same `harden`, `harden-define` and `harden-deliver` loop, with every decision written down and approved by a human.

What the direct path loses is usage data, the evidence that tells `harden` what to drop. The brief replaces it with a written scope and out-of-scope list that the product owner signs. That list keeps the build small.

## Steps

1. **Load config.** Read `.thought-viber/config.yaml`. If it's missing, create it as `probe` does in its first step (Load config). A new project often starts here, before any beta exists.

2. **Prepare the boundary.** Direct features usually build the core that betas will attach to, so set up what `vibe` and `gate` rely on now, while there's little code to rearrange. Skip whatever is already in place.
   - **Public interface convention.** If config's `public_interface_convention` is empty, agree with the user how durable code will mark what betas may import: a folder per module (`src/<module>/public/**`) or each module's entry file (`packages/<module>/index.ts`). Pick what fits the stack's habits, and record it in config. What matters is that public code is identifiable by path and never shares a file with internal code, so `public_interfaces` can name it with a glob.
   - **Boundary check.** If the repo has no boundary check yet, add it as `vibe` describes in its "Before building" step 2, wired into CI and the normal test command, as its own reviewed change. With no betas yet it passes trivially; it's there so durable code never starts importing from `<beta_root>/`, and so the first beta meets a check that already runs.

3. **Check the feature belongs on this path.** Ask the user: *"Is there a result that would make you not build this?"*
   - **No**: the feature will be used regardless. Continue.
   - **Yes, if users don't use it or don't want it**: there's a demand question, and that's what a beta answers. Say so and suggest `probe`, however clear the spec feels. Knowing *what* to build isn't the same as knowing *whether* to.
   - **Yes, for another reason** (cost, a technical unknown): record it under "What would stop us"; it isn't a reason for a beta.

   Then name the reason it's direct:
   - **foundation:** every user goes through it, or other features build on it (auth, users, accounts, the core schema, the first public interfaces);
   - **obligation:** a contract, a regulation or a partner requires it;
   - **safety floor:** it must change something on config's `safety_floor` list or under its `floor_paths`, so it can't be a beta.

   For a safety-floor feature that also has a demand question, say so: the honest options are a narrower beta that stays off the floor (`probe`), or accepting the bet without evidence. Let the product owner choose.

4. **Check past lessons.** Read `.thought-viber/LEARNINGS.md` if it exists. If a similar feature was reaped as a beta, show that line: it may mean the feature isn't as certain as it looks.

5. **Pin down the brief with the user.** Ask until each of these is concrete:
   - **Outcome:** what is true for users when this is done.
   - **Behaviors:** the list of things it does, each short and testable from the outside. Each one becomes acceptance tests in a spec, and `harden` assigns every behavior to exactly one intent.
   - **Out of scope:** what it won't do. Name the tempting additions (social sign-in, 2FA, admin screens) explicitly. Anything not listed under Behaviors is out.
   - **Users and systems affected**, and **constraints** (compliance, deadlines, compatibility, performance).
   - **Interfaces for betas:** durable interfaces this feature provides that future betas may call, each at a path that follows `public_interface_convention`, or "none".
   - **Safety floor:** which floor items it touches, so each spec flags the right policy owners, and the code paths that will implement them (for example `src/auth/**`). Those paths go into `floor_paths`.
   - **Owner:** the product owner who approves the brief, the decomposition and each spec.

6. **Write** `<artifacts_root>/<feature>/brief.md` from `templates/brief.md` with `status: proposed`.

7. **Gate.** The product owner confirms brief.md. On confirmation set `confirmed` to today and hand off to `harden`, which splits it into intents.

## Rules

- No beta, no flag, no telemetry events, no trial and no verdict. Release the feature through the team's normal process.
- A direct feature doesn't count against `max_live_betas`; there's no beta code in the repo.
- After confirmation, changes to Behaviors or Out of scope go in the "Changes after confirmation" section with date, reason and approver, never as silent edits. A behavior that grows a demand question during the build ("should we also…?") becomes its own `probe`, not a new line in the brief.
- One brief per feature. If it's really two features, write two briefs.
- The intent that builds an interface for betas adds its path to config's `public_interfaces`, and the intent that builds safety-floor code adds its paths to `floor_paths`, each in the same change. Until then a beta could reach code gate doesn't know to protect.
