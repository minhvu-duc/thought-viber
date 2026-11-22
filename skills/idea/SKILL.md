---
name: idea
description: Start a thought-viber beta. Use when someone wants to try a feature idea fast and see whether users actually use it before building it properly. Writes down who sees it, what counts as real use, how long to watch and what would make it worth keeping, in one beta file.
---

# idea

A beta answers one question: will people use this? `idea` writes that question down before any code exists, along with what would count as a yes, so the verdict can't drift to fit the numbers.

## Steps

1. **Set up, first time only.** If `.thought-viber/config.yaml` is missing, create it from `templates/config.yaml` with the user: where beta code goes, how flags are switched, and where usage data can be read.
   - If the app has no feature flag or event tracking yet, say so. Every beta needs both, and they're permanent code, so they're built once through the SDLC flow (`plan`) before the first `vibe`.
   - Add the beta-code rule from `templates/agent-rules.md` to the project's agent instructions file (`AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, whichever the project uses), with the real beta folder filled in. This is what makes every agent working in the repo, not only these skills, warn before building on beta code. Show the user the text before adding it.

2. **Is this a bet?** Ask: *"If nobody used it, would you still build it?"* If yes (sign-in, the core schema, something a contract requires), there's nothing to watch. Send them to `plan`.

3. **Check past lessons.** If `.thought-viber/LEARNINGS.md` has a similar idea that was reaped, show that line before going on.

4. **Pin it down with the user**, briefly:
   - **The idea:** what it is, in a sentence or two, and the smallest version that would show whether people want it.
   - **Who sees it:** everyone, a segment, or a % of users.
   - **What counts as real use:** the interaction to count, and the event(s) that record it. Prefer repeat use over first clicks: a "Beta" label draws curious one-time clicks.
   - **Keep if:** a rough bar, for example "a third of the people who see it use it twice in two weeks". Write it now, before anyone has seen numbers. It can change later, with a note saying why.
   - **Watch for:** how many days (default from config).
   - **Owner:** who makes the call at verdict.

5. **Write** `.thought-viber/betas/<feature>.md` from `templates/beta.md` with `status: idea`, and hand off to `vibe`.

One bet per beta. If the idea is really two, write two.
