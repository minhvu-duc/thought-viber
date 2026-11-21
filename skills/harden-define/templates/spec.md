# Spec NN: <title>

- From intent: `intent.md` (accepted <date>)
- Policy skills applied: <list>

## Behaviors
<each behavior: trigger, steps, result; link the acceptance test>

## Interfaces and contracts
<APIs, events, function signatures, versioning>

## Data
<entities, fields, constraints, migration from beta_<feature>_*>

### Cutover (data intent only)
<how the beta code still running switches to the durable store in this change set, so users' data lives in one place; or, if it can't, how both stores stay consistent until the last slice lands>

## Errors and edge cases
-

## Non-functional
<performance, security, accessibility, observability>

## Flags
| Concern | Policy | Owner | Resolution |
|---|---|---|---|

## Changes to locked acceptance tests
<!-- only with product owner approval: test — change — reason — approved by -->
