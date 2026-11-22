# Gate: <feature>

- Date: YYYY-MM-DD
- Commit: <sha>
- Result: **PASS | FAIL**

## Deterministic checks
| Check | Result | Notes |
|---|---|---|
| Boundary (imports) | | |
| Changes outside beta root are marked wiring only | | |
| Floor changes are only accepted, marked code touches from probe.md | | |
| Schema changes only add beta_<feature>_* tables/columns | | |
| No secrets | | |
| Dependency audit | | |
| Flag exists, default off, flag-off test | | |
| Telemetry events fire | | |
| Acceptance tests run by normal test command; all green, none skipped | | |
| Durable prep merged separately (if any) | | |

## Independent review
| Severity | Finding | Location | Status |
|---|---|---|---|
| | | | open / fixed / accepted |

## Accepted findings
<!-- severity — finding — accepted by — reason -->
