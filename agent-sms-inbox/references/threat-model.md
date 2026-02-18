# Threat Model For SMS Agents

## Primary Threats

- Prompt injection embedded in user SMS
- Social engineering for privileged operations
- Data exfiltration through unconstrained tool calls
- Replay abuse using repeated inbound events
- User impersonation through weak identity checks

## Threat Controls

- Never execute instructions directly from free text.
- Classify action requests separately from natural language intent.
- Gate side effects behind policy and identity verification.
- Bind high-risk actions to explicit user confirmation tokens.
- Log all tool invocations with actor, reason, and policy decision.
