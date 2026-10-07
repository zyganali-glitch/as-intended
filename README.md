# As Intended

**Financial Execution Assurance for autonomous finance**

**Tagline:** Financial execution that stays within mandate.

**Core question:** Did the money move as intended—and is that still true?

**Mental model:** Intent → Authority → Reality

This repository is the canonical project workspace for **As Intended**, built for the Airwallex AI Agents / Agentic Banking Hackathon.

## Current status

**PRE-BUILD FREEZE is active.**

Before official build authorization is independently re-verified, this repository may contain:
- governance,
- planning,
- architecture,
- threat modelling,
- evidence policy,
- cost/security policy,
- competition application material,
- API feasibility notes.

It must not contain competition implementation code presented as hackathon build work.

## Product thesis

As Intended is not an AI CFO, payment bot, AP replacement, agent wallet, policy-only guardrail, reconciliation dashboard, or multi-agent showcase.

It is a **Financial Execution Assurance** layer for autonomous finance.

Initial wedge: **cross-border supplier obligations**.

Airwallex is the financial rail/system of record. As Intended is the assurance layer that binds:
- intent,
- authority,
- actual financial reality,
- economic exposure,
- reconciliation,
- recovery.

## Canonical controls

- Provider/API success ≠ business outcome.
- Transaction state ≠ obligation state.
- Idempotency ≠ economic-exposure protection.
- Ambiguous money-out → quarantine before retry.
- Current source-of-truth outranks historical success.
- Assurance is evidence-bound, current and revocable.
- Deterministic code owns authority, limits, request identity, exposure, execution gates, lifecycle, reconciliation and recovery.
- Simulation must be visibly separated from real sandbox effects.

See `AGENTS.md`, `plans/AS_INTENDED_MASTER_EXECUTION_PLAN.md`, and `docs/HANDOFF.md`.
