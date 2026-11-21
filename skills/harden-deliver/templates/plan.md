# Plan NN: <title>

- From spec: `spec.md`
- Approved by: <engineer>, <date>

## Files that change
| File | Change |
|---|---|

## Order of work
1. Commit locked acceptance tests (failing).
2.

## Beta removal in this slice
- Reroute: <wiring change from beta to durable>
- Delete: <beta files and marked wiring lines covered by this intent>
- Data cutover (data intent only): <migration, beta code switched to the durable interface, how we prove nothing writes beta_<feature>_* afterwards>

## Risks
| Risk | Mitigation |
|---|---|

## Tests that prove it
- Locked acceptance tests:
- New unit/integration tests:

## Done when
```
<exact command>   # expected healthy output
```

## Divergences
<!-- date — what changed from this plan — why (update in the same commit as the code) -->
