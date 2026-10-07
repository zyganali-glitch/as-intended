# Product Contract

## Locked identity

**As Intended**

Category: **Financial Execution Assurance**

Descriptor: **Financial Execution Assurance for autonomous finance**

Tagline: **Financial execution that stays within mandate.**

Core question: **Did the money move as intended—and is that still true?**

Mental model: **Intent → Authority → Reality**

## Product thesis

As Intended lets a finance operator delegate bounded money movement while preserving the ability to answer, with current evidence:

- what was intended,
- what authority existed,
- what financial effect actually occurred,
- what unresolved economic exposure still exists,
- whether prior assurance remains valid,
- whether recovery is currently permitted.

Initial wedge: **cross-border supplier obligations**.

Airwallex is the financial rail/system of record.
As Intended is the assurance layer.

## Signature concept: Economic Exposure

Economic Exposure is the unresolved amount of financial risk that may already have been created even when the application does not yet know the final provider outcome.

Example:
- supplier obligation: EUR 8,000
- transfer submitted
- client response lost / outcome uncertain
- unresolved exposure: EUR 8,000
- a second EUR 8,000 transfer must not be opened merely with a different request ID

## Signature demo behavior

### Ambiguous execution
A real sandbox transfer may be submitted while the application deliberately simulates loss of the client response.

UI must visibly label:
`SIMULATED CLIENT RESPONSE LOSS`

The sandbox side-effect remains real.

Expected assurance behavior:
- quarantine new money movement,
- read Airwallex back,
- determine actual effect,
- prevent duplicate payment.

### Revocable assurance
If fresh financial evidence contradicts a prior success:
- withdraw prior assurance,
- reopen/reconcile the obligation,
- block unsafe duplicate exposure,
- allow replacement only when prior exposure is proven safe to replace and the original mandate still authorizes recovery.

Do not hard-code provider status names or exact transitions until current sandbox behavior is independently verified.
