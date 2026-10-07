# QA Verification Contract

## Independent verification workflow

For every Antigravity report:

1. identify exact task/batch
2. inspect remote `main`
3. verify ancestry from last independently VERIFIED SHA
4. inspect exact changed files
5. inspect relevant source/config/tests
6. validate semantics and financial invariants
7. verify Airwallex/live evidence where applicable
8. check secrets, cost, donors, simulation/live separation and future leakage
9. inspect scoped tests and exact-SHA CI where present
10. issue PASS or REPAIR

## PASS

Use:
`P-XX.XX — PASS ✅`

Only after independent verification.

## REPAIR

Use:
`P-XX.XX — REPAIR ❌`

Then provide one consolidated same-scope repair prompt.

## Batch policy

Batch roughly 4–5 consecutive same-phase tasks when:
- coherent
- reversible
- low-risk
- no live mutation
- no security/authority/exposure boundary change

Single-task QA is preferred for:
- phase boundary
- first live Airwallex path
- money mutation
- security/authority/exposure change
- donor-code introduction
- irreversible/billing risk
- dependency on QA-established facts

## Evidence discipline

Edited ≠ validated.
Local commit ≠ remote closure.
NOT_RUN ≠ PASS.
Mock/fixture ≠ live.
Recorded-live ≠ current-live.
Green tests ≠ integration proof.
