---
name: vibe
description: Build and ship a thought-viber beta fast. Use after idea, to vibe-code the feature behind a flag with usage events, keeping its code in the beta folder where possible and marking every line it has to put elsewhere, so the beta is easy to reap.
---

# vibe

Speed is the goal, and so is being easy to delete. Nobody needs to review this code line by line. What matters is that reaping it takes minutes: turn the flag off, delete the folder, delete the marked lines.

## Keep it reapable (best effort)

- **Beta folder first.** Put the feature's code in `<beta_root>/<feature>/`.
- **Mark what has to live elsewhere.** A route, a menu entry, a hook into existing code, a migration: keep each as small as you can and put a marker comment on the line above it, in the file's own comment syntax:

  ```
  // thought-viber:beta:<feature> — written for the <feature> beta; may be removed. Don't build on it.
  app.route("/quick-reply", quickReply)
  ```

  For more than one line, wrap the block:

  ```
  // thought-viber:beta:<feature> begin — written for the <feature> beta; may be removed. Don't build on it.
  ...
  // thought-viber:beta:<feature> end
  ```

- **Changing existing behavior:** leave the old path intact and branch on the flag, so flag off is exactly today's behavior and reaping means deleting the branch. If you have to edit existing code in place, note how to undo it in Footprint.
- **Data** goes under its own names, `beta_<feature>_*` tables, columns or keys. Don't alter existing columns; if there's no other way, mark the migration and note how to undo it.
- **Don't build on other betas.** If this beta would need another beta's code, follow the beta-code rule in the project's agent instructions: warn the user and suggest a way around it.
- **Sensitive areas.** If the beta touches sign-in, permissions, payments, personal data or secrets, tell the user before writing it and keep the change small. It isn't blocked; the user just knows.

## Build

1. Read `.thought-viber/betas/<feature>.md` and config. Set `status: building`.
2. Build the smallest version from the Idea section. Cut anything that doesn't help show whether people use it.
3. Put it behind flag `beta.<feature>`, off by default. Flag off hides it everywhere. Show a "Beta" label wherever users meet it.
4. Send the events listed under "Real use".
5. Write a few black-box tests of the main behaviors, through HTTP, UI or CLI rather than beta internals. They keep the beta working, and if it's kept, they're the starting acceptance tests for the permanent version.
6. Check: tests pass, flag off hides the feature, every event fires.
7. Fill **Footprint** in the beta file: every marked place outside the beta folder, the data it creates and any dependencies it added. This is reap's list.

## Ship

1. Merge and deploy through the normal pipeline with the flag off.
2. When the user says go, turn the flag on for "Who sees it", or give them the exact switch to flip.
3. Set `status: live`, `live_since` and `watch_until`, and hand off to `watch`.
