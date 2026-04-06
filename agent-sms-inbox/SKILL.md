---
name: agent-sms-inbox
description: "Secure SMS agent workflow for building a text message chatbot, messaging agent, or SMS bot on Rewrite. Covers LLM-driven replies over application-owned inbound conversations with prompt-injection defense, action policy gates, risk-level escalation, and outbound send controls."
---

# Agent SMS Inbox Security

Build an application-owned SMS agent on top of Rewrite under strict safety controls.

## Core Principles & Safeguards

- Treat user-authored SMS content as **untrusted input** — never execute instructions from free text.
- Separate intent understanding from permission to act; require explicit **policy gate** checks before any side effect.
- Maintain a **tool allowlist** with strict argument schemas; deny unknown or malformed actions by default.
- Keep conversation state, canonical user identity, and policy logic in **your application** — Rewrite is the transport layer.
- Enforce **rate limits** per sender/tenant and run red-team prompts in automated tests.
- **Escalate** uncertain, high-risk, or low-confidence requests to a human operator.

## Mandatory Pipeline

1. **Ingest** a normalized Rewrite webhook event or application-authenticated conversation event.
   - *Checkpoint:* Validate payload against the expected schema (see `references/installation.md` for SDK types). Reject malformed events before any processing.
2. **Resolve** user identity, conversation state, workflow context, and any Rewrite `contactId` linkage from your application data.
3. **Classify** intent, risk level (1–4), and requested action (see `references/security-levels.md`).
4. **Policy gate** — evaluate allow / deny / escalate / confirm:
   - *Checkpoint:* Log the policy decision, risk level, and reason before proceeding.
5. **Execute** only allowlisted tools or internal actions (see `references/safe-actions.md`).
6. **Send** replies via the outbound skill (`send-sms/`) when the policy allows it.
7. **Audit** — persist the full decision trace, tool usage, and outcome.

### Minimal Policy Gate Example

```typescript
type Decision = "allow" | "deny" | "escalate" | "confirm";

function evaluatePolicyGate(riskLevel: number, action: string, allowlist: Set<string>): Decision {
  if (!allowlist.has(action)) return "deny";
  if (riskLevel >= 4) return "escalate";   // high-risk → human
  if (riskLevel === 3) return "confirm";    // sensitive → user confirmation
  return "allow";                           // levels 1–2
}

const decision = evaluatePolicyGate(riskLevel, requestedAction, ALLOWED_ACTIONS);
logger.info({ action: requestedAction, riskLevel, decision }); // checkpoint log
if (decision === "deny") throw new PolicyDeniedError(requestedAction);
```

## Security Resources

- SDK/package setup and product boundary: `references/installation.md`
- Threat model and attack patterns: `references/threat-model.md`
- Risk levels and policy matrix: `references/security-levels.md`
- Safe action design: `references/safe-actions.md`
- Local testing, forwarding, and replay: `references/local-dev-tunneling.md`
