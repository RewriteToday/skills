---
name: send-sms
description: Production SMS delivery workflow for Rewrite. Use when Codex needs to send transactional or campaign SMS, implement batching, enforce idempotency, handle delivery states, and integrate Rewrite SDKs (Node, Go, Types) or REST.
---

# Send SMS With Rewrite

Implement outbound SMS in a production-safe way.

## Workflow

1. Install client dependencies from `references/installation.md`.
2. Choose transport:
- SDK-first (Node or Go) for app-native integration.
- REST-first for language-agnostic systems.
3. Build payload validation for destination, sender identity, and content.
4. Add idempotency and retry policy before scaling traffic.
5. Persist message IDs and map provider events back to application state.

## Delivery Design Rules

- Make every send operation idempotent.
- Treat delivery as asynchronous; do not rely on synchronous send response as final state.
- Use queue-backed fan-out for high volume and campaign workloads.
- Record structured metadata for traceability: tenant, campaign, user, workflow step.

## Resource Map

- Install and SDK selection: `references/installation.md`
- Single-send implementation patterns: `references/single-sms-examples.md`
- Batch and queue patterns: `references/batch-sms-examples.md`
- Production guardrails: `references/best-practices.md`
- Delivery and webhook lifecycle: `references/delivery-lifecycle.md`

## Implementation Checklist

1. Define `SendSms` contract at app boundary.
2. Validate and normalize destination numbers.
3. Attach idempotency key per business action.
4. Send through Rewrite client.
5. Persist outbound record with provider message ID.
6. Update status from webhook events.
7. Trigger compensating logic for permanent failures.
