# Financial Invariants

These are non-negotiable.

## Authority

A valid mandate must bind at minimum:
- beneficiary/counterparty identity
- amount
- currency
- validity window
- relevant constraints
- recovery/retry permission where applicable

Model output is never authority by itself.

## Exposure

`request_id` deduplication and Economic Exposure Lock solve different problems.

A new request ID must not bypass unresolved economic exposure.

Durable local reservation must exist before money-out.

## Ambiguity

Unknown/timeout/client-response-loss after a money-out attempt is not permission to retry.

Required behavior:
1. quarantine
2. read back
3. reconcile
4. classify exposure
5. only then decide whether recovery is permitted

## Truth hierarchy

Current financial source-of-truth outranks:
- previous application success,
- historical local state,
- model explanation,
- executor report.

## Assurance

Assurance must carry:
- evidence source
- freshness
- observation time
- current status
- revocation capability

A previous satisfied state may be downgraded by fresh contradictory evidence.

## Provider normalization

Domain code must not depend on unverified Airwallex status strings.

Provider-specific states are normalized in the Airwallex adapter.

## Simulation honesty

Fault injection may simulate:
- lost client response
- local persistence failure
- network uncertainty

But simulated conditions must be visibly and structurally separated from real Airwallex sandbox effects.
