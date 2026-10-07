# Architecture Direction

## V1 shape

Minimal single-service architecture:

- browser-native HTML/CSS/JS frontend
- FastAPI backend
- Pydantic domain/contracts
- SQLite persistence
- httpx Airwallex adapter
- pytest verification
- one model interface
- deterministic financial control path

## Separation of responsibility

### Model may
- interpret operator intent
- propose structured mandate terms
- explain decisions
- summarize reconciliation state

### Deterministic code owns
- authority validation
- limits
- reserve checks
- request identity
- durable reservation
- economic exposure
- execution gates
- lifecycle state
- read-back
- reconciliation
- recovery permission

## Canonical financial truth

V1 truth owner:
**deliberate Airwallex read-back/polling**

Webhooks:
- future accelerator
- not canonical truth owner in V1

Financial Transactions:
- may corroborate
- do not become a brittle core dependency unless sandbox feasibility proves otherwise

## Core domain objects

Planned conceptual objects:
- Obligation
- Mandate
- Authority
- ExecutionAttempt
- EconomicExposure
- ReconciliationObservation
- AssuranceState
- RecoveryDecision
- DecisionRecord / ExecutionRecord

These names may be refined only if semantics remain intact.

## UX surfaces

- Overview
- Mandates
- Reconciliation
- Activity
- Records

Primary frame:
**Intent / Authority / Reality**
