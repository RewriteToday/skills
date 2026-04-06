---
name: send-sms
description: Production SMS delivery workflow for Rewrite. Use when the agent needs to send transactional or campaign SMS, manage Rewrite contacts or segments, implement batching, enforce idempotency, use templates and scheduling, and integrate the official Rewrite SDKs, REST client, CLI, or ecosystem packages.
---

# Send SMS With Rewrite

Implement outbound SMS against the current public Rewrite API contract.

## Workflow

1. Install client dependencies from `references/installation.md`.
2. Choose one target shape:
- direct number: `to`
- stored contact: `contact` as contact ID or exact contact name
3. Choose one content shape:
- inline body: target plus `content`
- template body: target plus `templateId` plus `variables`
4. Use `/contacts` when recipients need stable metadata such as `name`, `channel`, or per-contact `tags`.
5. Use `/segments` and `/segments/:id/contacts` to maintain reusable audiences. There is no direct segment-send endpoint; list the segment contacts, then call `/messages` or `/messages/batch`.
6. Add optional `tags`, `scheduledAt`, and `segmentation` only when required by the flow.
7. Attach `Idempotency-Key` to every create/send action.
8. Persist the accepted message ID plus the returned `analysis`.
9. Register webhooks before production rollout and treat delivery as asynchronous.
10. Use batch sends only for 1 to 100 messages per request.

## Delivery Design Rules

- Use Brazilian `+55` E.164 numbers on the public API unless the project explicitly supports international numbers.
- Prefer `contact` over raw `to` when the same recipient will be reused, tagged, or segmented.
- If you target `contact` by name, keep the name unique and stable per project because lookup is exact.
- Sending with raw `to` still ensures a Rewrite contact exists for that phone number.
- Prefer templates for repeated or locale-aware flows.
- Keep OTP and transactional content short and watch segmentation counts.
- Retry only `429` and transient `5xx`.
- Treat `400`, `401`, and commercial `403` blocks as non-retryable until fixed.
- Do not require `message.delivered` for critical workflows; it is still WIP in the public docs.

## Quick Example

Single SMS send via REST:

```bash
curl -X POST https://api.rewrite.com/messages \
  -H "Authorization: Bearer $REWRITE_API_KEY" \
  -H "Idempotency-Key: $(uuidgen)" \
  -H "Content-Type: application/json" \
  -d '{"to": "+5511999999999", "content": "Your code is 4821"}'
```

## Resource Map

- Install, SDK selection, CLI, and ecosystem packages: `references/installation.md`
- Contacts, segments, and audience orchestration: `references/contacts-and-segments.md`
- Single-send implementation patterns: `references/single-sms-examples.md`
- Batch and queue patterns: `references/batch-sms-examples.md`
- Template i18n rules: `references/template-i18n.md`
- Delivery and webhook lifecycle: `references/delivery-lifecycle.md`
- Production guardrails: `references/best-practices.md`

## Output Contract

Persist or return the accepted response. Expected shape:

```json
{
  "id": "msg_abc123",
  "status": "accepted",
  "to": "+5511999999999",
  "createdAt": "2026-04-06T12:00:00Z",
  "analysis": { "encoding": "GSM-7", "characters": 22, "segments": 1 },
  "idempotencyKey": "550e8400-e29b-41d4-a716-446655440000",
  "tags": []
}
