# Pre-Build Readiness & Zero-Spend Checklist

**Classification:** OPERATOR_CHECKLIST / DOCS_ONLY  
**Status:** Pre-Build Governance Control — No Runtime Actions  
**Created:** 2026-10-07  
**Personal Spend Ceiling:** **$0.00 USD**

---

## A. Before Build Phase Checklist (Pre-Build Period)

| Item # | Check Item | Requirement / Criteria | Current Status | Operator Sign-off |
| :---: | :--- | :--- | :--- | :---: |
| **A-1** | **HackerEarth Registration** | Registration submitted and confirmed on platform. | CONFIRMED | [X] |
| **A-2** | **Idea Submission Published** | Title: "As Intended — Financial Execution Assurance for Autonomous Finance" published. | CONFIRMED | [X] |
| **A-3** | **Repository Initialization** | Canonical repository `zyganali-glitch/as-intended` active on branch `main` with verified governance. | CONFIRMED | [X] |
| **A-4** | **Airwallex Sandbox Account Readiness** | Operator has registered for free developer sandbox on Airwallex web app; credentials ready. | READY | [X] |
| **A-5** | **No Production Account Dependency** | Project architecture explicitly forbids production Airwallex account or credentials. | ENFORCED | [X] |
| **A-6** | **Zero Real Money Movement** | System design uses synthetic sandbox balances only; zero actual financial settlement rails. | ENFORCED | [X] |
| **A-7** | **No PAYG Cloud Accounts** | No AWS/GCP/Azure pay-as-you-go infrastructure connected; local process execution only. | ENFORCED | [X] |
| **A-8** | **No Card or Deposit Commitments** | Operator personal credit cards or bank deposits are completely detached from project. | ENFORCED | [X] |
| **A-9** | **Secret-Storage Plan Defined** | Backend-only `.env` template defined, `.gitignore` confirmed active. | CONFIGURED | [X] |
| **A-10** | **Local Ollama Fallback Ready** | Local LLM runtime available for offline, zero-cost prompt interpretation. | AVAILABLE | [X] |
| **A-11** | **Partner Credits Inactive Until Observed** | Hackathon-provided LLM/cloud credits treated as unavailable until physically verified in dashboard. | ENFORCED | [X] |

---

## B. Build-Unfreeze Day Checklist (Execution Gate)

*Execute this checklist strictly on the official build start day before any P-01 implementation task.*

- [ ] **Step 1: Execute P-00.03 First**
  - Conduct independent web/platform search for updated hackathon rules, dates, and deliverables.
  - Record findings in `plans/AS_INTENDED_MASTER_EXECUTION_PLAN.md` audit note.
- [ ] **Step 2: Re-check Official Dates & Competition Timing**
  - Verify official Build Phase opening date/time.
  - Confirm idea submission phase is concluded and implementation is permitted.
- [ ] **Step 3: Verify Remote `main` Baseline**
  - Run `git fetch origin main` and confirm HEAD matches the last independently VERIFIED SHA.
- [ ] **Step 4: Verify Airwallex Sandbox Account Accessibility**
  - Operator logs in manually to `api-demo.airwallex.com` dashboard in browser.
  - Verify sandbox dashboard is operational and synthetic funds are visible.
- [ ] **Step 5: Verify Local Sandbox API Credentials**
  - Confirm `.env` exists locally containing `AIRWALLEX_CLIENT_ID` and `AIRWALLEX_API_KEY`.
  - Confirm `.env` is ignored by git (`git check-ignore -v .env`).
  - Do NOT output credentials in shell, logs, or chat.
- [ ] **Step 6: Verify Sandbox Endpoint Configuration**
  - Confirm base URL in config points strictly to `https://api-demo.airwallex.com` or `https://api.sandbox.airwallex.com`.
  - Confirm production URL `api.airwallex.com` is absent or barred by assertion.
- [ ] **Step 7: Verify Zero Billing Risk**
  - Confirm all tooling, dependencies, and environments remain at $0.00 cost profile.
- [ ] **Step 8: Issue Formal BUILD UNFREEZE Decision**
  - Record unfreeze decision in `docs/HANDOFF.md`.
  - Only then proceed to Task P-01.01.

---

## C. Secret-Handling Protocol

1. **Backend Runtime Only:**
   - All credentials (`AIRWALLEX_CLIENT_ID`, `AIRWALLEX_API_KEY`) must be loaded strictly by backend Pydantic settings via environment variables.
2. **Gitignore Protection:**
   - `.env`, `.env.*`, and private key files must be confirmed ignored before saving credentials.
3. **No Browser Exposure:**
   - Frontend API endpoints must never serialize or proxy API keys or provider bearer tokens.
4. **No LLM Prompt Injection:**
   - System and user prompt templates must never include raw authorization headers or API keys.
5. **Redacting Logger:**
   - Outbound HTTP logging must apply regex filters to mask `Authorization: Bearer [REDACTED]` and `x-api-key: [REDACTED]`.
6. **No Public Evidence Leaks:**
   - Terminal recordings, screenshots, and demo videos must obscure any account numbers or client IDs.
7. **Immediate Rotation Protocol:**
   - If an API key or token is accidentally exposed in git history or chat, the operator MUST immediately revoke it in the Airwallex Developer console and generate a new key.

---

## D. Cost-Stop Conditions (Immediate Halt Triggers)

The development team and automated tools **MUST IMMEDIATELY STOP** work and notify the operator if any of the following occur:

1. **Personal Spend Trigger:** Any tooling, platform, or API charge that would incur $> \$0.00$ on operator credit cards or personal accounts.
2. **Payment Method Prompt:** Any third-party service (LLM, cloud hosting, API aggregator) prompts for a credit card or billing account to continue execution.
3. **Credit Depletion Alert:** Hackathon partner credits are exhausted and system attempts to fall back to pay-as-you-go (PAYG) billing.
4. **Production Account Alert:** Any Airwallex API response indicates interaction with a live commercial production account rather than the developer sandbox.
5. **Billing Ambiguity:** Any uncertainty regarding whether an API call or deployment is billable. Fail closed immediately.
