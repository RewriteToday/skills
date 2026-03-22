# Threat Model For SMS Agents

## Primary Threats

- Prompt injection embedded in user-authored messages
- Social engineering for privileged operations
- Data exfiltration through unconstrained tool calls
- Replay or duplicate execution caused by repeated Rewrite webhook events
- User impersonation through weak identity checks in the application layer
- Over-trusting Rewrite transport metadata as if it were end-user identity proof

## Threat Controls

- Never execute instructions directly from free text.
- Classify action requests separately from natural language intent.
- Gate side effects behind policy and identity verification.
- Bind high-risk actions to explicit user confirmation tokens.
- Log all tool invocations with actor, reason, and policy decision.
- Keep webhook verification, payload validation, and user authorization as three separate checks.
