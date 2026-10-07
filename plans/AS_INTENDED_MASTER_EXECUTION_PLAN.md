# As Intended — Master Execution Plan

This plan is canonical below remote `main` and independently verified evidence.

Antigravity must execute only explicitly authorized tasks.

---

# PHASE P-00 — Governance Bootstrap & Pre-Build Control

## P-00.01 — Bootstrap governance spine
**Status:** DONE — independently PASS

Scope:
- place approved starter-pack files into canonical repository
- preserve contents except for path-safe normalization if needed
- commit to `main`
- push remote
- report exact remote SHA and changed files

Forbidden:
- product implementation code
- new dependencies
- provider calls
- sandbox mutation
- CI that executes product logic

Acceptance:
- only governance/planning files introduced
- PRE-BUILD FREEZE clearly visible
- remote `main` updated
- exact SHA independently verifiable

Hard stop after this task.

## P-00.02 — Reconcile canonical bootstrap state
**Status:** DONE — independently PASS

Independent QA only.

Verify:
- remote `main`
- exact changed files
- no implementation leakage
- no secrets
- no donor-code reuse
- no paid infrastructure

Output:
- PASS or REPAIR
- establish first independently VERIFIED SHA

## P-00.03 — Competition-rule refresh before build
**Status:** PENDING

Timing:
Immediately before implementation authorization.

Research current official sources for:
- Build Phase start
- eligibility
- final deliverables
- sandbox access
- partner credits
- technical restrictions
- repository/submission requirements

No code.

Acceptance:
- build authorization is evidence-based
- unresolved rule conflict blocks implementation

## P-00.04 — Official Airwallex feasibility dossier
**Status:** DONE — independently PASS

Scope:
- Research sandbox architecture, base URL, and auth protocol from official Airwallex docs
- Survey required V1 endpoint families (auth, balances, global accounts, beneficiaries, transfers, status simulation)
- Document request_id semantics, 7-day retention, and why idempotency != economic exposure protection
- Document transfer lifecycle, late failure after PAID, and simulation capabilities
- Contrast scope against Payment Ops Incident Commander starter kit
- Document open feasibility gates (corridor, currency, schema, method) as DESIGN / DOCS_ONLY
- No API calls; no runtime dependencies

## P-00.05 — Financial threat model
**Status:** DONE — independently PASS

Scope:
- Model 22 financial and operational threat/failure classes
- Define assets/invariants at risk, failure paths, deterministic controls, required evidence, and fail-closed behaviors
- Map threats to planned mitigation phases
- Explicitly cover model authority hallucination, stale mandates, beneficiary substitution, exposure leaks, and simulation masquerade
- No code

## P-00.06 — Demo evidence & proof plan
**Status:** DONE — independently PASS

Scope:
- Design evidence chains for flagship demo Story A (Ambiguous execution under response loss) and Story B (Revocable assurance under late settlement failure)
- Define user-visible indicators, deterministic engine state, evidence classes (DESIGN, TEST, SANDBOX_LIVE, SIMULATED)
- Require explicit visual badging for simulated response drops
- Map expected fields against Decision/Execution Record schema
- No code

## P-00.07 — Sandbox / zero-spend readiness checklist
**Status:** DONE — independently PASS

Scope:
- Define pre-build operator readiness checks (HackerEarth, sandbox readiness, $0 spend boundary)
- Establish build-unfreeze day checklist gated by P-00.03
- Define secret-handling protocol (backend-only, .env ignored, redacting logger, zero browser/prompt leaks)
- Define cost-stop conditions ($0 personal spend, no PAYG fallback, no card commitments)
- No login or API calls

## P-00.08 — Pre-build architecture decision register
**Status:** DONE — independently PASS

Scope:
- Record 15 LOCKED non-negotiable architectural and financial decisions
- Record 9 OPEN feasibility decisions deferred until live sandbox evidence
- Define required evidence, earliest task, and unverified assumptions for each open item
- Preserve Intent → Authority → Reality mental model and single-agent direction
- No code

---

# PHASE P-01 — Airwallex Sandbox Feasibility

Starts only after explicit BUILD UNFREEZE (blocked while PRE-BUILD FREEZE remains active; P-01 and later implementation phases remain unauthorized).

## P-01.01 — Establish sandbox access and zero-spend preflight
Verify:
- sandbox account access
- usable credentials
- no production account dependency
- no personal-spend requirement
- secret handling path

Single-task QA gate.

## P-01.02 — Read-only Airwallex connectivity
Implement minimal backend-only authenticated read path.

Requirements:
- no browser secrets
- no money mutation
- provider adapter boundary
- structured error handling
- tests with mocks plus independently verified live sandbox read

Single-task QA gate.

## P-01.03 — Discover least-friction demo corridor
Use current sandbox capabilities to determine:
- usable funding path
- source currency
- destination currency
- beneficiary requirements
- transfer method
- simulation support

Do not lock corridor before evidence.

## P-01.04 — Verify transfer lifecycle vocabulary
Independently observe/document current sandbox behavior.

Deliver:
- provider-state inventory
- normalized-domain-state proposal
- explicit unknown/unmapped handling

No domain code may depend on undocumented assumptions.

## P-01.05 — Verify sandbox simulation capabilities
Establish what Airwallex currently supports for:
- transfer state simulation
- post-success failure/return-style behavior
- timing
- repeatability

Classify evidence:
SANDBOX_LIVE vs RECORDED_SANDBOX.

Phase gate:
No execution architecture is finalized until P-01 is PASS.

---

# PHASE P-02 — Domain Contracts

## P-02.01 — Define Obligation contract
Fields and invariants for the business obligation.

## P-02.02 — Define Mandate / Authority contract
Must bind:
- beneficiary
- amount
- currency
- validity
- constraints
- recovery permission

## P-02.03 — Define ExecutionAttempt and request identity
Separate:
- provider request identity
- attempt identity
- obligation identity

## P-02.04 — Define EconomicExposure contract
Must represent unresolved exposure independently of request-id deduplication.

Single-task QA gate because exposure semantics are competition-defining.

## P-02.05 — Define ReconciliationObservation and AssuranceState
Assurance must be:
- evidence-bound
- fresh/stale aware
- revocable

## P-02.06 — Define RecoveryDecision
Deterministic reasons only.

## P-02.07 — Domain adversarial tests
Cover:
- malformed authority
- mismatched beneficiary
- amount/currency drift
- expired mandate
- duplicate request identity
- unresolved exposure
- stale evidence
- contradictory fresh evidence

---

# PHASE P-03 — Persistence & Exposure Safety

## P-03.01 — SQLite schema design
Minimal durable schema for:
- obligations
- mandates
- reservations
- execution attempts
- observations
- assurance
- records

## P-03.02 — Durable reservation before money-out
Reservation must commit before provider mutation.

Single-task QA gate.

## P-03.03 — request_id deduplication
Must not be conflated with exposure lock.

## P-03.04 — Economic Exposure Lock
Block new money-out while unresolved exposure exceeds mandate safety.

Single-task QA gate.

## P-03.05 — crash/restart recovery tests
Prove local restart does not reopen unsafe exposure.

---

# PHASE P-04 — Airwallex Adapter

## P-04.01 — Provider normalization boundary
Map observed Airwallex states to domain states.

Unknown provider state must fail safe.

## P-04.02 — Idempotent transfer mutation wrapper
Use provider-supported request identity.

No retry policy yet.

## P-04.03 — Canonical read-back/poll path
Read-back is V1 truth owner.

## P-04.04 — Reconciliation identity lookup
Prove application can locate prior effect after uncertain client response.

Single-task QA gate with live sandbox evidence.

---

# PHASE P-05 — Authority & Execution Gate

## P-05.01 — Structured mandate creation
Model may propose; deterministic parser/validator owns accepted structure.

## P-05.02 — Authority validation
Check beneficiary, amount, currency, validity and constraints.

Single-task QA gate.

## P-05.03 — Reserve-floor / limit gate
Deterministic financial constraint enforcement.

## P-05.04 — Execution eligibility state machine
Money-out allowed only when all gates pass.

## P-05.05 — adversarial authority tests
Cover prompt/model attempts to exceed authority.

---

# PHASE P-06 — Ambiguous Execution & Quarantine

## P-06.01 — Ambiguous-result classification
Timeout/client-response-loss after mutation is not retry permission.

## P-06.02 — Quarantine state
Block new exposure.

## P-06.03 — Simulated client response loss fault injection
UI and records must clearly label:
`SIMULATED CLIENT RESPONSE LOSS`

Simulation must not replace real sandbox side-effect.

## P-06.04 — Read-back reconciliation
Discover actual provider effect using prior identity/context.

## P-06.05 — Duplicate-payment prevention proof
Real sandbox proof:
- first mutation exists
- client uncertainty simulated
- no second unsafe payment
- read-back resolves outcome

Single-task live QA gate.

---

# PHASE P-07 — Revocable Assurance

## P-07.01 — Assurance freshness model
Track evidence age and currentness.

## P-07.02 — Fresh contradiction handling
Fresh provider evidence may revoke prior satisfied assurance.

## P-07.03 — Obligation reopen/reconcile behavior
Transaction lifecycle and obligation lifecycle remain separate.

## P-07.04 — Recovery permission gate
Replacement only if:
- prior exposure is proven safe
- mandate remains valid
- recovery is permitted
- all current constraints pass

Single-task QA gate.

## P-07.05 — Revocation adversarial tests
Cover:
- stale contradiction
- incomplete evidence
- replayed observation
- fresh mismatch
- historical-success bias

---

# PHASE P-08 — Records & Audit UX

## P-08.01 — Decision/Execution Record persistence
No donor-specific terminology.

## P-08.02 — Record evidence freshness
Expose observation time/source.

## P-08.03 — Record exposure history
Show when exposure opened/resolved.

## P-08.04 — Record assurance revocation
Explain why earlier assurance changed.

---

# PHASE P-09 — Finance-Native UI

## P-09.01 — Overview surface
Show:
- current obligation outcome
- unresolved exposure
- evidence freshness
- authority state

## P-09.02 — Mandates surface
Show Intent and Authority.

## P-09.03 — Reconciliation surface
Show Reality and evidence.

## P-09.04 — Activity surface
Chronological operational events.

## P-09.05 — Records surface
Audit-ready Decision/Execution Records.

## P-09.06 — UX truthfulness tests
Simulation/live separation must be obvious.

---

# PHASE P-10 — Agent Interface

## P-10.01 — Single model interface
No multi-agent framework.

## P-10.02 — Intent interpretation
Model proposes structured terms only.

## P-10.03 — Decision explanation
Model explains deterministic outcomes without owning them.

## P-10.04 — Model-unavailable fallback
Core safety and financial state remain functional without model access.

## P-10.05 — Zero-spend provider selection
Use only verified free/local resources unless operator explicitly approves otherwise.

---

# PHASE P-11 — Demo Signature: Ambiguous Execution

## P-11.01 — Prepare bounded supplier obligation
Sandbox-safe demo setup.

## P-11.02 — Execute real sandbox transfer
Single-task live mutation gate.

## P-11.03 — Trigger simulated client response loss
Must be visibly labeled.

## P-11.04 — Prove quarantine
No new money-out.

## P-11.05 — Read back Airwallex
Recover actual effect.

## P-11.06 — Prove no duplicate economic exposure
Live evidence plus record artifact.

Phase closure requires independent QA.

---

# PHASE P-12 — Demo Signature: Reality Changes

## P-12.01 — Establish sandbox-supported lifecycle path
Use only currently verified provider behavior.

## P-12.02 — Observe apparent success
Record current assurance.

## P-12.03 — Cause/observe fresh contradictory financial evidence
Use official sandbox simulation facilities where supported.

Single-task live QA gate.

## P-12.04 — Revoke assurance
Prior success must be withdrawn.

## P-12.05 — Reopen/reconcile obligation
Block unsafe exposure.

## P-12.06 — Safe recovery if permitted
Replacement only when evidence proves it is safe and mandate still authorizes it.

Phase closure requires independent QA.

---

# PHASE P-13 — Security, Privacy & Failure Hardening

## P-13.01 — secret leakage audit
## P-13.02 — browser boundary audit
## P-13.03 — model boundary audit
## P-13.04 — malformed provider response tests
## P-13.05 — persistence failure tests
## P-13.06 — concurrency/exposure race tests
## P-13.07 — stale-read and replay tests
## P-13.08 — zero-spend audit

---

# PHASE P-14 — Competition Closure

## P-14.01 — Re-verify official submission requirements
Current sources only.

## P-14.02 — README and setup instructions
Truthful, reproducible, no fake evidence.

## P-14.03 — Demo script
Lead with:
1. bounded authority
2. ambiguous money-out
3. quarantine
4. read-back
5. duplicate prevention
6. assurance revocation
7. safe recovery

## P-14.04 — <5 minute video plan if still required
Re-verify duration first.

## P-14.05 — final evidence audit
Classify every material claim.

## P-14.06 — final donor/provenance audit
## P-14.07 — final security/secrets audit
## P-14.08 — exact-SHA final QA
## P-14.09 — submission freeze
No last-minute unverified changes.

---

# Execution rules

- No task may self-expand.
- Remote `main` is canonical.
- First live Airwallex mutation is always independently gated.
- Any authority/exposure/security semantic change is independently gated.
- Any donor-code introduction is independently gated.
- No task may weaken tests to obtain green status.
- Evidence labels must remain truthful.
