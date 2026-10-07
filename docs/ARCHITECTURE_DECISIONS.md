# Pre-Build Architecture Decision Register (ADR)

**Classification:** DESIGN / DOCS_ONLY  
**Status:** Pre-Build Architecture Governance — No Implementation Code  
**Created:** 2026-10-07  
**Guiding Principle:** Lock structural and financial invariants now; defer provider-specific parameters until live evidence is gathered.

---

## 1. Locked Decisions (Non-Negotiable)

| Decision ID | Area | Decision Summary | Rationale & Invariant Bound |
| :---: | :--- | :--- | :--- |
| **ADR-01** | **Product Identity** | **Financial Execution Assurance** category. | As Intended is neither a generic AI CFO, AP workflow bot, wallet, nor simple policy guardrail. It guarantees that autonomous money movement remains within authorized mandate and verifies whether financial reality matches intent. |
| **ADR-02** | **Mental Model** | **Intent → Authority → Reality** pipeline. | Disentangles business obligation (Intent) from financial permission (Authority) and rail settlement (Reality). Prevents conflation of intent with authority. |
| **ADR-03** | **Initial Wedge** | **Cross-border supplier obligations**. | High-stakes cross-border disbursements suffer from banking opacity, currency drift, settlement delays, and ambiguous timeouts, making execution assurance essential. |
| **ADR-04** | **Agent Architecture** | **Single-agent architecture only**. | Bloated multi-agent frameworks add non-deterministic chatter, latency, and failure modes. A single focused agent interface coordinates intent parsing; all financial control is deterministic. |
| **ADR-05** | **Control Separation** | **Deterministic financial control path**. | Free-form LLM prose never creates financial authority. Deterministic code strictly owns authority validation, limits, reserve checks, request IDs, durable reservations, exposure locks, execution gates, lifecycle, and recovery. |
| **ADR-06** | **Rail Runtime** | **Airwallex REST API as runtime**. | Direct HTTPS communication via standard `httpx` client directly against official Airwallex REST endpoints. No middleware proxies or unofficial SDK wrappers. |
| **ADR-07** | **MCP Boundary** | **MCP for development/testing only**. | Airwallex Developer MCP may assist local developer exploration and testing workflows. It is strictly forbidden as a runtime production dependency of the application. |
| **ADR-08** | **Canonical Truth** | **Deliberate read-back/polling as V1 truth owner**. | In distributed financial systems, push notifications may be lost or delayed. Active, authenticated HTTP GET polling directly to Airwallex owns canonical financial reality in V1. |
| **ADR-09** | **Webhooks Role** | **Webhooks as non-owning accelerators**. | Webhooks provide low-latency hints to trigger polling, but the engine never trusts an unverified inbound webhook payload as authoritative truth without read-back corroboration. |
| **ADR-10** | **Persistence** | **SQLite for V1 durable storage**. | Single-process, ACID-compliant local database. Zero cloud database configuration or cost overhead. WAL mode provides high-throughput concurrent reads. |
| **ADR-11** | **Reservation Safety** | **Durable reservation before money-out**. | An obligation must commit a local database reservation and lock exposure before the outbound HTTP request is dispatched to Airwallex. Prevents orphan payouts on local crash. |
| **ADR-12** | **Exposure Invariant** | **`request_id` dedup $\ne$ Economic Exposure Lock**. | Provider idempotency protects against byte-level payload replays, but does not prevent agents from opening duplicate exposure under new request IDs. As Intended enforces an application-level Economic Exposure Lock. |
| **ADR-13** | **Assurance Semantics**| **Assurance is evidence-bound and revocable**. | Provider success at $T_0$ (`PAID`) does not guarantee eternal completion. Contradictory fresh evidence at $T_1$ (late return/rejection) revokes assurance and reopens the obligation. |
| **ADR-14** | **UX Architecture** | **Finance-native UX surfaces**. | Browser-native HTML/CSS/JS interface centered around: Overview, Mandates, Reconciliation, Activity, and Audit Records. |
| **ADR-15** | **Cost Target** | **Personal spend ceiling: $0.00 USD**. | Zero personal expenditure across cloud hosting, LLM tokens, or financial deposits. Zero PAYG fallback. |

---

## 2. Open Feasibility Decisions (Deferred Until Evidence)

The following design decisions are intentionally left open and will only be resolved once live sandbox evidence is collected in Phase P-01.

| Open Item ID | Decision Subject | Evidence Required to Close | Earliest Phase / Task | What Must NOT Be Assumed Before Evidence |
| :---: | :--- | :--- | :---: | :--- |
| **OPEN-01** | **Airwallex API Version to Pin** | Live inspection of response headers and behavior across endpoints under specific date versions (e.g. `2024-01-31`). | **P-01.02** | Do not assume all endpoints behave identically under a global header without live read-back verification. |
| **OPEN-02** | **Transfer Status Normalization Map** | Comprehensive inventory of raw status strings (`PROCESSING`, `SENT`, `PAID`, `FAILED`, `CANCELLED`) observed in live sandbox payloads. | **P-01.04** | Do not hardcode domain state machine mappings before observing actual sandbox status transitions. |
| **OPEN-03** | **Demo Corridor & Currencies** | Inspection of default synthetic currencies and funding balances in the operator's sandbox account (`GET /api/v1/balances/current`). | **P-01.03** | Do not lock a specific corridor (e.g. USD $\rightarrow$ EUR vs USD $\rightarrow$ SGD) before confirming available funds and routing rules. |
| **OPEN-04** | **Beneficiary Schema Structure** | Sandbox schema validation response for counterparty bank details in the chosen corridor. | **P-01.03** | Do not assume IBAN is mandatory if a domestic clearing corridor with routing numbers is selected. |
| **OPEN-05** | **Transfer Payment Method** | Availability of `LOCAL` vs `SWIFT` methods returned by Airwallex payment method validation API. | **P-01.03** | Do not assume local clearing is enabled for all currencies in the test environment. |
| **OPEN-06** | **FX Conversion in Flagship Demo** | Determining if multi-currency conversion (`POST /api/v1/fx/...`) is necessary or if single-currency cross-border transfer provides cleaner evidence. | **P-01.03** | Do not mandate complex FX quote/conversion flows if direct cross-border transfer proves sufficient. |
| **OPEN-07** | **Sandbox Simulation Sequence for Late Failure** | Observed latency and payload behavior of `simulate_status_transition` API when forcing `PAID` $\rightarrow$ `FAILED`. | **P-01.05** | Do not assume status transitions occur synchronously or trigger webhooks instantly. |
| **OPEN-08** | **Model Provider Selection** | Verification of free hackathon credits (e.g. Gemini/Anthropic) vs latency and performance of local Ollama runtime. | **P-05.01** | Do not assume paid cloud LLM access is active or free until verified in account. |
| **OPEN-09** | **Role of Financial Transactions Endpoint** | Timeliness and detail of `/api/v1/financial_transactions` vs `/api/v1/pa/transfers/{id}` for corroborating balance debits. | **P-01.04** | Do not make Financial Transactions a critical path dependency if transfer read-back is self-contained. |
