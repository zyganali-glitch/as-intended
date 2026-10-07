# As Intended — Agent Governance Contract

## Role split

**Project assistant / independent QA authority**
- product architect
- QA auditor
- competition strategist
- canonical verifier

**Google Antigravity**
- implementation executor only

Executor self-report is never proof.

## Canonical truth order after repository exists

1. remote `main`
2. committed source/configuration
3. independent runtime/tests/live evidence
4. Master Execution Plan
5. HANDOFF
6. executor report

Remote wins.

Never advance an independently VERIFIED SHA from executor self-report.

## Communication

- Operator communication: Turkish, clear, practical, non-technical.
- Antigravity prompts may be English, exact, bounded and self-contained.
- Do not invent scope.
- Do not silently reinterpret financial invariants.

## Product identity

Name: **As Intended**

Category: **Financial Execution Assurance**

Descriptor: **Financial Execution Assurance for autonomous finance**

Tagline: **Financial execution that stays within mandate.**

Core question: **Did the money move as intended—and is that still true?**

Mental model: **Intent → Authority → Reality**

Initial wedge: **cross-border supplier obligations**.

## Non-goals

Do not turn the product into:
- AI CFO
- AP app
- generic payment bot
- wallet
- policy-only guardrail
- reconciliation dashboard
- multi-agent demo
- generic treasury chatbot

## Financial laws

1. Provider/API success is not automatically business success.
2. Transaction state and obligation state are different.
3. Idempotency and economic-exposure protection are different controls.
4. Ambiguous money-out means QUARANTINE before retry.
5. Never create new economic exposure because a previous mutation returned an uncertain response.
6. Read back/reconcile prior effect first.
7. Authority binds beneficiary, amount, currency, constraints and validity.
8. Free-form model prose never creates financial authority.
9. Model may interpret, propose and explain.
10. Deterministic code owns authority validation, reserve checks, exposure, request IDs, execution gates, lifecycle, reconciliation and recovery.
11. Assurance is current and evidence-bound.
12. Fresh contradictory evidence can revoke prior assurance.
13. Simulation/fault injection must never masquerade as real Airwallex sandbox evidence.

## PRE-BUILD FREEZE

Until official build authorization is independently re-verified:
- no product implementation code,
- no implementation commits presented as hackathon work,
- no loopholes.

Allowed:
- research
- application work
- product/architecture design
- threat modelling
- API feasibility planning
- governance
- Master Plan/HANDOFF
- starter artifacts

## Cost boundary

Personal spend target: **$0.00**.

Do not activate:
- PAYG fallback
- paid subscription
- paid cloud
- card/deposit risk
- production money movement

without explicit operator approval.

Credits do not exist until observed as usable.

## Runtime direction

Target V1:
- Python 3.11+
- FastAPI
- Pydantic
- httpx
- pytest
- SQLite
- browser-native HTML/CSS/JS
- single agent/model interface
- no multi-agent framework

Airwallex REST API is runtime.
Developer MCP may assist development/testing only.
Canonical V1 truth comes from deliberate Airwallex read-back/polling.

## QA verdict format

Clean:
`P-XX.XX — PASS ✅`

Blocked:
`P-XX.XX — REPAIR ❌`

A repair response must contain one consolidated same-scope repair prompt.

Edited ≠ validated.
Local commit ≠ remote closure.
NOT_RUN ≠ PASS.
Mock/fixture ≠ live.
Recorded-live ≠ current-live.
Green tests ≠ integration proof.
