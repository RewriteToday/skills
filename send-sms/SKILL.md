---
name: send-sms
description: Production SMS delivery workflow for Rewrite. Use when the agent needs to send transactional or campaign SMS, implement batching, enforce idempotency, use templates and scheduling, and integrate the official Rewrite SDKs, REST client, CLI, or ecosystem packages.
---

# Send SMS With Rewrite

Implement outbound SMS against the current public Rewrite API contract.

## Workflow

1. Install client dependencies from `references/installation.md`.
2. Choose one send shape:
- inline body: `to` plus `content`
- template body: `to` plus `templateId` plus `variables`
3. Add optional `tags`, `scheduledAt`, and `segmentation` only when required by the flow.
4. Attach `Idempotency-Key` to every create/send action.
5. Persist the accepted message ID plus the returned `analysis`.
6. Register webhooks before production rollout and treat delivery as asynchronous.
7. Use batch sends only for 1 to 100 messages per request.

## Delivery Design Rules

- Use Brazilian `+55` E.164 numbers on the public API.
- Prefer templates for repeated or locale-aware flows.
- Keep OTP and transactional content short and watch segmentation counts.
- Retry only `429` and transient `5xx`.
- Treat `400`, `401`, and commercial `403` blocks as non-retryable until fixed.
- Do not require `message.delivered` for critical workflows; it is still WIP in the public docs.

## Resource Map

- Install, SDK selection, CLI, and ecosystem packages: `references/installation.md`
- Single-send implementation patterns: `references/single-sms-examples.md`
- Batch and queue patterns: `references/batch-sms-examples.md`
- Template i18n rules: `references/template-i18n.md`
- Delivery and webhook lifecycle: `references/delivery-lifecycle.md`
- Production guardrails: `references/best-practices.md`

## Output Contract

Persist or return:

- Rewrite message ID
- acceptance timestamp
- current known status
- message analysis: encoding, characters, segment count
- applied idempotency key
- business tags or correlation identifiers
