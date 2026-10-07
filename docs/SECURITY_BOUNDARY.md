# Security Boundary

## Secrets

Airwallex credentials/tokens must remain:
- backend/runtime only
- outside browser code
- outside model prompts unless strictly necessary and safely redacted
- outside logs
- outside public evidence
- outside committed repository history

## Browser boundary

Browser must never receive:
- Airwallex client secrets
- private API credentials
- unrestricted financial authority material

## Model boundary

The model must not:
- hold financial execution authority
- generate its own permission to pay
- bypass deterministic gates
- receive secrets by default
- decide exposure safety

## Production boundary

No production money/accounts unless explicitly required by competition and separately authorized by operator.

## Logging

Logs must avoid:
- secrets
- full credentials
- sensitive provider tokens
- unnecessary personal/financial data

Use synthetic/demo counterparties where practical.
