---
feature: <slug>
status: idea            # idea | building | live | killed | reaped | kept | permanent
owner: <who decides at verdict>
created: YYYY-MM-DD
flag: beta.<slug>
live_since:             # when the flag turned on for users
watch_until:            # live_since + watch days
---

# Beta: <feature name>

## Idea
<what it is, and the smallest version that shows whether people want it>

## Who sees it
<everyone / segment / % rollout>

## Real use
- Counts as use: <the interaction>
- Events: `beta.<slug>.<event>` — <when it fires, properties>

## Keep if
<rough bar, written before anyone has seen numbers>

## Footprint
<!-- Filled by vibe: everything outside beta/<slug>/ that reap has to undo. -->
| File | What it does | Marked | How to undo (edits to existing code) |
|---|---|---|---|

- Data: <beta_<slug>_* tables, columns, keys>
- Dependencies added: <or none>

## Depended on by
<!-- Later code that uses this beta's code anyway, marked thought-viber:uses-beta:<slug>.
     reap and transition must deal with each one. -->

## Watch log
<!-- watch appends a dated snapshot here, with the queries it used. -->

## Verdict
<!-- decision — decided by — date — why -->

## Notes
<!-- YYYY-MM-DD — change to the bar, audience or watch window — why -->
