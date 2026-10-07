# Airwallex API Feasibility Dossier (Pre-Build Research)

**Classification:** DESIGN / DOCS_ONLY  
**Status:** Pre-Build Research Only — No API Calls Made  
**Research Date:** 2026-10-07  
**Official Sources Consulted:**
- Airwallex Developer Documentation: https://www.airwallex.com/docs
- Airwallex Authentication API: https://www.airwallex.com/docs/api#/Authentication
- Airwallex Balances API: https://www.airwallex.com/docs/api#/Balances
- Airwallex Beneficiaries API: https://www.airwallex.com/docs/api#/Beneficiaries
- Airwallex Transfers API: https://www.airwallex.com/docs/api#/Transfers
- Airwallex Simulation API: https://www.airwallex.com/docs/api#/Simulation
- Airwallex Transfer Status Simulation Guide: https://www.airwallex.com/docs/payouts__simulate-transfer-status-transition

---

## 1. Sandbox Architecture & Environment Boundary

### Sandbox Base URLs
- **Current Officially Documented Sandbox Host:** `https://api.sandbox.airwallex.com` (documented standard for developer sandbox API calls).
- **Historical / Legacy Demo Host:** `https://api-demo.airwallex.com` (referenced in older documentation and legacy integration guides; retained for historical context only, not canonical for V1).
- **Production Base URL:** `https://api.airwallex.com` (STRICTLY FORBIDDEN during hackathon/pre-build; personal spend ceiling $0.00).

### Authentication Protocol
- **Endpoint:** `POST /api/v1/authentication/login`
- **Request Headers:**
  - `x-client-id: <SANDBOX_CLIENT_ID>`
  - `x-api-key: <SANDBOX_API_KEY>`
  - `Content-Type: application/json`
- **Response Shape:**
  - `token`: Short-lived JWT bearer token.
  - `expires_at`: UTC ISO timestamp indicating expiration.
- **Token Lifetime:** Typically **30 minutes** (1,800 seconds).
- **Subsequent Request Header:**
  - `Authorization: Bearer <token>`
- **Token Handling Law:**
  - Tokens are held in-memory in backend runtime only.
  - Token is reused until `expires_at` (minus safe skew window); clients must NOT re-login per individual request to avoid rate limiting.
  - Zero browser exposure: client credentials and bearer tokens NEVER cross the network boundary to frontend code.

### Production vs Sandbox Boundary
- Sandbox credentials cannot execute real-world financial settlement.
- Sandbox balance is synthetic; deposits are simulated via API or dashboard.
- Production accounts, cards, and bank routing are strictly decoupled. No production account or card is configured or permitted.

---

## 2. Required V1 Endpoint Families (Docs-Only Survey)

*All endpoint definitions below are researched from official documentation; zero live calls executed.*

### Authentication Family
- `POST /api/v1/authentication/login` — Exchange Client ID and API Key for temporary bearer token.

### Balances Family
- `GET /api/v1/balances/current` — Query available, pending, and reserved balances across supported currencies. Essential for pre-flight reserve verification before creating economic commitments.

### Global Account Deposit Simulation (Sandbox Only)
- `POST /api/v1/simulation/deposit/create` — Simulates inbound funds / deposits into demo global accounts without real money movement.

### Beneficiaries Family
- `POST /api/v1/beneficiaries/create` — Create counterparty beneficiary profile binding bank details, country, and entity information.
- `GET /api/v1/beneficiaries` — Query and list existing beneficiaries.
- `GET /api/v1/beneficiaries/{id}` — Retrieve counterparty record by ID for authority verification.
- `POST /api/v1/beneficiaries/validate` — Validate beneficiary details against scheme/corridor requirements before execution.
- `POST /api/v1/beneficiary_api_schemas/generate` — Generate required beneficiary schema and validation rules for a specific country and currency combination.

### Transfers Family
- `POST /api/v1/transfers/create` — Submits a transfer execution attempt.
  - Body parameters: `request_id`, `source_currency`, `transfer_amount`, `beneficiary_id` (or nested `beneficiary`), `payment_method`, `reason`.
- `GET /api/v1/transfers/{id}` — Primary read-back polling endpoint to obtain current financial state from Airwallex.
- `GET /api/v1/transfers` with query filters (e.g. `request_id`, `created_at`) — Lookup transfer when provider ID was lost during ambiguous client timeout (exact filtering behavior to be verified in P-01 live feasibility).
- `POST /api/v1/transfers/validate` — Validate transfer parameters prior to mutation.

### Transfer Simulation Family (Sandbox Only)
- `POST /api/v1/simulation/transfers/{id}/transition` — Transition transfer status in sandbox.
  - Parameter: `status` / target transition state (`PROCESSING`, `SENT`, `PAID`, `FAILED`, `CANCELLED`).
  - In official docs, a transition to `FAILED` may automatically transition or lead to `CANCELLED`.
  - Essential tool for reproducing late settlement rejection in controlled testing.

### FX Rates / Quotes / Conversions (Deferred / Optional)
- `POST /api/v1/fx/quotes/create`, `POST /api/v1/fx/conversions/create` — Optional for cross-currency corridor if source currency differs from transfer currency.
- *Open Status:* Deferred until demo corridor feasibility is proven in P-01.

---

## 3. `request_id` Semantics & Economic Exposure Safety

### Official Airwallex Behavior
- **Idempotency Field:** `request_id` passed in mutation payload.
- **Deduplication Window:** Rolling **7 days**.
- **Duplicate Behavior:** Submitting a transfer with an identical `request_id` within 7 days results in rejection as a duplicate or return of the previously created transfer representation, without double-debiting funds.
- **Ambiguity Recovery:** When a network disconnect or timeout occurs during `transfers/create`, querying by `request_id` or retrying with the *exact same* `request_id` enables safe re-query.

### Why `request_id` is NOT Sufficient for Economic Exposure Safety
In autonomous agent architectures, provider idempotency alone fails to protect enterprise capital:
1. **New Request ID Vulnerability:** If an agent, timeout handler, or retry loop generates a fresh `request_id` (e.g. UUIDv4 generated on each retry), the provider treats it as an entirely new transaction and executes a double payment.
2. **Ignorance of Obligation Semantics:** The provider idempotency layer has no awareness of underlying supplier obligations, contractual ceilings, or invoice validity.
3. **Absence of Reserve Checking:** Idempotency does not ensure the account has reserved funds committed locally to avoid race conditions across concurrent tasks.
4. **Economic Exposure Lock Requirement:** As Intended introduces a deterministic **Economic Exposure Lock**. Any ambiguous response places the obligation into `QUARANTINE`. No retry or recovery may be initiated—even with the same or different request ID—until deliberate read-back determines whether exposure exists.

---

## 4. Transfer Lifecycle & State Observations

### Documented Provider Status Model
Airwallex documentation indicates the standard transfer progression:
```
[IN_APPROVAL / SCHEDULED]
         │
         ▼
    PROCESSING
         │
         ▼
       SENT
         │
         ▼
       PAID  ──(Late Failure)──►  FAILED
         │
         ▼
   (Terminal)
```
- **`PROCESSING`**: Transfer is accepted, funded, and currently processed.
- **`SENT`**: Transfer dispatched to external clearing/banking partner.
- **`PAID`**: Banking partner reports successful delivery of funds.
- **`FAILED` / `CANCELLED`**: Rejection or cancellation.

### Critical Discovery: Late Failure After `PAID`
Official Airwallex documentation explicitly highlights:
> **"PAID is not always final."** A transfer marked `PAID` may transition to `FAILED` hours or days later if the recipient bank or local clearing network subsequently rejects or returns the funds.

This official provider invariant directly validates the core thesis of **As Intended**:
- **Assurance must be revocable.** An application that treats provider HTTP 200 or initial `PAID` status as immutable business completion creates dangerous divergence between accounting records and financial reality.

### Sandbox Simulation Capabilities
- The sandbox allows explicit simulation of status transitions using the status simulation endpoint: `POST /api/v1/simulation/transfers/{id}/transition`.
- Supported transition states include `PROCESSING`, `SENT`, `PAID`, `FAILED`, and `CANCELLED`.
- Official documentation notes that a transition to `FAILED` may automatically transition to `CANCELLED`.
- This provides the concrete capability to reproduce both ambiguous execution and late revocations under deterministic test conditions.

---

## 5. Starter-Kit Alignment & Scope Differentiation

### Contrast with "Payment Ops Incident Commander"
The hackathon idea catalog includes "Payment Ops Incident Commander". As Intended is adjacent to operational tooling but materially broader and structurally distinct:

| Dimension | Payment Ops Incident Commander | As Intended |
| :--- | :--- | :--- |
| **Lifecycle Phase** | Post-incident / Reactive | Full lifecycle: Intent → Authority → Reality |
| **Primary Actor** | Alert bot / Human ops assistant | Autonomous execution assurance controller |
| **Authority Model** | None (reads alerts and logs) | Formal, bounded financial mandate validation |
| **Exposure Protection** | None (observes already-failed transfers) | Pre-mutation durable reservation & quarantine |
| **Truth Model** | Event/Alert triage | Independent Airwallex read-back & revocable assurance |
| **Output** | Incident tickets, summaries, slack alerts | Verified settlement state, exposure locks, recovery gates |

---

## 6. Open Feasibility Gates (Pending Sandbox Live Verification in P-01)

The following items are intentionally **UNRESOLVED** and must NOT be locked during pre-build:
1. **Canonical Sandbox Host:** Current official documentation supports `https://api.sandbox.airwallex.com` as primary sandbox host; live ping in P-01.01 will verify connectivity (legacy `api-demo.airwallex.com` retained only as historical fallback).
2. **Demo Corridor & Currency:** Exact currency pair (e.g. USD -> EUR, USD -> SGD, or single-currency transfer) to be selected based on sandbox balance and beneficiary simplicity in P-01.03.
3. **Beneficiary Schema:** Exact mandatory fields (IBAN vs routing vs SWIFT) dependent on selected corridor in P-01.03.
4. **Transfer Method:** Local clearing (e.g. SEPA, ACH, FAST) vs SWIFT dependent on corridor feasibility.
5. **Exact Provider Status Normalization:** Raw provider status strings will be cataloged from live sandbox payloads in P-01.04 before domain mapping is frozen.

All claims in this document remain classified as **DESIGN / DOCS_ONLY**.
