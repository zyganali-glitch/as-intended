# Financial Threat Model & Security Assurance Matrix

**Classification:** DESIGN / DOCS_ONLY  
**Status:** Pre-Build Governance Specification — No Implementation Code  
**Created:** 2026-10-07  
**Guiding Principle:** Deterministic code governs financial authority, exposure, and truth; model prose is never authority.

---

## Threat & Failure Matrix

| ID | Threat / Failure Class | Asset / Invariant at Risk | Failure Path | Required Deterministic Control | Required Evidence | Fail-Closed Behavior | Mitigation Phase / Task |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **TM-01** | **Model Invents Financial Authority** | Treasury Capital; Mandate Invariant | LLM hallucinates or generates prose approving a transfer without explicit operator mandate. | Pydantic mandate parser rejecting free-form text; strict deterministic schema validation. | Validated `Mandate` entity record adhering to deterministic schema. | Immediate rejection; transfer execution pipeline refuses to execute. | P-02.02, P-05.02 |
| **TM-02** | **Stale Mandate Reused** | Mandate Validity Invariant | Valid past mandate is resubmitted after expiration window has elapsed. | Monotonic UTC clock check verifying `valid_until > utcnow()` prior to any mutation. | Mandate expiration timestamp vs server time audit record. | Execution blocked; mandate flagged as `EXPIRED`. | P-02.02, P-02.07 |
| **TM-03** | **Beneficiary Substitution** | Counterparty Identity Invariant | Prompt injection or payload manipulation redirects payout to malicious account. | Deterministic counterparty matching binding beneficiary ID, IBAN/account, and country to mandate. | Beneficiary equality verification record. | Hard stop; audit alert emitted; zero funds dispatched. | P-02.02, P-02.07 |
| **TM-04** | **Amount / Currency Drift** | Value Invariant | Decimal precision error, currency conversion drift, or currency switch (e.g. USD vs EUR). | Exact integer/minor-unit currency amount matching; cross-currency mismatch blocked unless authorized. | Currency and amount comparison log. | Abort attempt; obligation marked `PARAMETER_MISMATCH`. | P-02.02, P-02.07 |
| **TM-05** | **`request_id` Replay** | Idempotency Integrity | An expired or reused request ID is submitted, risking collision or unexpected provider rejection. | Unique `attempt_id` mapped deterministically to a freshly generated, audited `request_id`. | Local database execution attempt record. | Replay rejected; new execution attempt required. | P-02.03, P-03.03 |
| **TM-06** | **Duplicate Request with Different `request_id`** | Economic Exposure Ceiling | Network timeout occurs; caller generates a new `request_id` and retries, creating double payment. | **Economic Exposure Lock**: Obligation level lock preventing new mutations while prior attempt unresolved. | Active `EconomicExposure` record with non-zero exposure. | New mutation strictly blocked; state transitions to `QUARANTINE`. | P-02.04, P-03.04 |
| **TM-07** | **Ambiguous Mutation Outcome** | Economic Exposure Invariant | Airwallex transfer POST times out or returns HTTP 5xx; client cannot determine if money moved. | Immediate transition of obligation to `QUARANTINE`; automatic read-back scheduling. | Incomplete `ExecutionAttempt` record awaiting reconciliation. | Retries blocked; human/automated intervention restricted to read-only polling. | P-02.04, P-04.03, P-06.01 |
| **TM-08** | **Local Persistence Failure After Provider Success** | Persistence / Truth Alignment | Provider completes payout (HTTP 200), but local SQLite database fails to commit attempt. | **Pre-mutation Durable Reservation**: Local row written before network request dispatched; recovery reconciles orphans. | Durable reservation record with status `DISPATCHED`. | Startup/recovery scanner detects uncommitted state and polls Airwallex read-back. | P-03.02, P-04.04 |
| **TM-09** | **Provider Success with Obligation Unsatisfied** | Business Outcome Invariant | Provider returns success, but payout applied to wrong invoice, wrong beneficiary, or partial amount. | Independent obligation reconciliation comparing provider reality against obligation contract. | `ReconciliationObservation` record evaluating full business terms. | Obligation remains `UNSATISFIED` despite provider HTTP 200. | P-02.01, P-04.03 |
| **TM-10** | **Late Provider Failure After Apparent Success** | Revocable Assurance Invariant | Airwallex returns `PAID`, but days later transitions to `FAILED` due to recipient bank return. | **Revocable Assurance Model**: Continuous or event-triggered read-back capable of revoking prior assurance. | Fresh `ReconciliationObservation` documenting status regression. | Prior assurance revoked; obligation reopened; exposure recalculated. | P-02.05, P-04.03, P-07.02, P-07.03 |
| **TM-11** | **Stale Evidence Overriding Fresh Evidence** | Source of Truth Invariant | Cached read-back from $T_0$ is accepted over newer observation at $T_1$ indicating failure. | Monotonic observation sequencing; observation timestamps and sequence counters enforce freshness. | Observation timestamp audit trail with freshness expiration window. | Stale cache discarded; fresh provider query enforced. | P-02.05, P-04.03, P-07.01 |
| **TM-12** | **Replayed Historical Observation** | Truth Integrity | Historical provider response replayed into reconciliation engine to simulate completion. | Nonce/timestamp verification and direct, authenticated outbound polling to Airwallex. | Direct, authenticated HTTP read-back response from Airwallex with verified observation timestamp. | Synthetic/stale response rejected. | P-02.05, P-04.03, P-13.07 |
| **TM-13** | **Concurrency Race Opening Duplicate Exposure** | Atomicity / Exposure Ceiling | Two concurrent agent workers process the same obligation simultaneously. | Durable pre-mutation reservation and atomic serialization before HTTP dispatch (e.g. SQLite immediate transaction / atomic reservation state). | Committed pre-mutation reservation record. | Second concurrent worker receives immediate lock conflict error and halts. | P-03.02, P-13.06 |
| **TM-14** | **Malicious / Accidental Retry Loop** | Rate & Capital Safety | Buggy loop continuously calls transfer endpoint on failure. | Bounded backoff and mandatory quarantine gate preventing autonomous mutations while exposure is unresolved. | Retry attempt counter in `ExecutionAttempt` record. | Retries blocked; obligation quarantined; recovery requires explicit deterministic gate and mandate authorization. | P-02.06, P-06.01, P-07.04 |
| **TM-15** | **Browser Secret Leakage** | Credential Security | Airwallex API key or bearer token exposed to browser client via API responses or frontend code. | Architecture barrier: Credentials stored only in backend `.env`; browser client communicates only with FastAPI. | Automated repository scan; frontend contract inspection confirming no token fields. | Application startup fails if client secrets are referenced in frontend assets. | P-01.01, P-13.02 |
| **TM-16** | **Model Secret Leakage** | Confidentiality Boundary | Prompt injection or context leak passes API credentials into LLM completion prompt. | LLM prompt templates sanitized; zero credentials or auth headers ever supplied to prompt context. | Prompt template unit test asserting absence of secret strings. | Sanitizer throws exception if string resembling API key appears in prompt payload. | P-10.01, P-13.03 |
| **TM-17** | **Logs / Public Evidence Secret Leakage** | Evidence Integrity | Raw authorization headers or API keys logged to console, disk, or demo screen. | Redacting HTTP logging adapter (`Authorization: Bearer [REDACTED]`, `x-api-key: [REDACTED]`). | Log audit test verifying regex redaction of all sensitive keys. | Build fails if unredacted secret patterns match logger output. | P-01.01, P-01.02, P-13.01 |
| **TM-18** | **Sandbox Simulation Falsely Represented as Real Evidence** | Evidence Honesty Policy | Synthetic fault injection or local mock presented as authentic Airwallex sandbox behavior. | Mandatory `EvidenceClass` tag (`DESIGN`, `TEST`, `SANDBOX_LIVE`, `RECORDED_SANDBOX`, `SIMULATED`) on all events. | UI badge and record field explicitly displaying evidence class. | Display without explicit simulation banner blocked. | P-06.03, P-09.06 |
| **TM-19** | **Donor-Code Provenance Contamination** | IP & Competition Integrity | Legacy donor code pasted without clean-room reimplementation or proper attribution. | Provenance registry enforcement; static scan for legacy donor namespaces. | Provenance checklist in `docs/DONOR_PROVENANCE.md`. | PR/commit blocked if unauthorized donor source detected. | P-00.02, P-14.06 |
| **TM-20** | **Production Endpoint / Credential Confusion** | Production Isolation | Developer accidentally runs against `api.airwallex.com` or production API keys. | Strict environment and hostname validation restricting network traffic to authorized sandbox domains; explicit startup assertion barring production host. | Hardcoded configuration assertion: domain must match `*demo*` or `*sandbox*`. | Process crashes immediately on startup if production domain is targeted. | P-01.01, P-13.08 |
| **TM-21** | **Paid-Service / PAYG Fallback** | Zero-Spend Policy ($0.00) | Cloud provider or paid LLM fails over to billable credit card or metered PAYG account. | Hard budget disablement; zero-cost local Ollama fallback; no billing details supplied. | Pre-build readiness zero-spend confirmation. | Process immediately fails closed on external billing trigger. | P-00.07, P-01.01, P-13.08 |
| **TM-22** | **Operator Approval Detached from Financial Parameters** | Governance Authority | Operator clicks generic "Confirm" button without seeing exact counterparty, amount, and fee. | Deterministic approval interface binding and displaying exact beneficiary, currency, and amount; requires explicit operator confirmation of complete terms before mandate acceptance. | Audit record capturing confirmed counterparty, currency, amount, and constraints. | Approval rejected if underlying terms changed between display and click. | P-02.02, P-05.01, P-05.02, P-09.02 |

---

## Defense-in-Depth Control Architecture

```
[ Operator Intent ]
         │
         ▼
[ Deterministic Authority Validation ] ──► (TM-01, TM-02, TM-03, TM-04, TM-22)
         │
         ▼
[ Durable Pre-Mutation Reservation ]  ──► (TM-08, TM-13)
         │
         ▼
[ Economic Exposure Lock ]            ──► (TM-06, TM-07, TM-14)
         │
         ▼
[ Airwallex Sandbox API Call ]         ──► (TM-15, TM-17, TM-20)
         │
         ├────────────────────────┐
         ▼                        ▼
[ Ambiguous Timeout ]     [ Provider HTTP 200 ]
         │                        │
         ▼                        ▼
    QUARANTINE               Read-Back Verification ──► (TM-09, TM-11, TM-12)
         │                        │
         └───────────┬────────────┘
                     ▼
        [ Continuous Assurance ]      ──► (TM-10: Revocable on Late Failure)
```
