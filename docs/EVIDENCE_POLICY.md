# Evidence and Live-vs-Simulation Policy

## Evidence classes

Every material claim must identify its class.

### DESIGN
Architecture or planned behavior only.

### TEST
Deterministic automated/local test evidence.

### SANDBOX_LIVE
Current independently observed Airwallex sandbox behavior.

### RECORDED_SANDBOX
Historical sandbox evidence retained for audit; not proof of current provider behavior.

### SIMULATED
Fault injection or synthetic behavior produced by As Intended.

## Rules

- Executor report is never proof.
- Mock/fixture is never live evidence.
- Recorded-live is not current-live.
- UI text must not imply a sandbox event occurred if it was simulated locally.
- Simulation labels must be explicit.
- Current live feasibility must be re-established when a task depends on provider behavior that may change.

## Revocable assurance evidence

Any assurance record should eventually include:
- evidence source
- provider object identity
- observed-at time
- freshness status
- normalized financial state
- contradiction reason if assurance was revoked
