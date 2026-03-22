# Security Levels And Policy Matrix

Use four levels for predictable handling.

## Level 1: Informational

- Examples: FAQ, delivery-status explanation, non-sensitive summaries
- Policy: auto-allow response generation
- Side effects: none

## Level 2: Low-Risk Transactional

- Examples: resend an OTP, confirm a known workflow step, update non-sensitive preferences
- Policy: allow with input validation and rate limits
- Side effects: limited, auditable

## Level 3: Sensitive

- Examples: account updates, PII-adjacent requests, schedule changes that affect billing or access
- Policy: require strong verification and explicit confirmation
- Side effects: restricted, reversible where possible

## Level 4: High-Risk

- Examples: payment actions, credential resets, security changes, data export, privileged admin flows
- Policy: deny autonomous execution and escalate to human
- Side effects: never executed directly by agent
