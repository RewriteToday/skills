---
name: agent-sms-inbox
description: Secure application-owned SMS agent workflow built on Rewrite outbound sends and Rewrite webhook events. Use when the agent needs to turn app-owned conversation inputs into LLM-driven replies while preventing prompt injection, enforcing action policies, gating risky operations, and escalating uncertain cases safely.
---

# Agent SMS Inbox Security

Build an application-owned SMS agent on top of Rewrite under strict safety controls.

## Core Principles

- Treat user-authored content as untrusted input.
- Separate content understanding from permission to execute actions.
- Require explicit policy checks before side effects.
- Keep the conversation state and identity model in your application.
- Escalate uncertain or high-risk interactions.

## Mandatory Pipeline

1. Ingest a normalized Rewrite webhook event or an application-authenticated conversation event.
2. Resolve user, workflow, conversation state, and any Rewrite `contactId` linkage from your application data.
3. Classify intent, risk level, and requested action.
4. Run the policy gate: allow, deny, escalate, or require confirmation.
5. Execute only allowlisted tools or internal actions.
6. Send replies asynchronously through the outbound skill (`send-sms/`) when the policy allows it.
7. Log the decision trace, tool usage, and policy outcome.

## Security Resources

- SDK/package setup and product boundary: `references/installation.md`
- Threat model and attack patterns: `references/threat-model.md`
- Risk levels and policy matrix: `references/security-levels.md`
- Safe action design: `references/safe-actions.md`
- Local testing, forwarding, and replay: `references/local-dev-tunneling.md`

## Required Safeguards

- Tool allowlist with explicit argument schemas.
- Runtime validation at the boundary for webhook/event payloads.
- Outbound action confirmation for sensitive operations.
- Rate limiting per sender and per tenant.
- Red-team prompts in automated tests.
- Human escalation for high-risk or low-confidence requests.
- Explicit separation between Rewrite transport identifiers such as `contactId` and your app's canonical user identity.
- Explicit documentation that Rewrite is the transport and event layer, while inbox state and policy live in your app.
