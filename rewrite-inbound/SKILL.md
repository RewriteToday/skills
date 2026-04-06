---
name: rewrite-inbound
description: Rewrite webhook ingestion workflow for your application. Use when the agent needs to configure Rewrite webhooks, verify signatures, parse and normalize event payloads, handle retries and dedupe, inspect delivery logs, and route delivery or OTP events safely.
---

# Rewrite Webhook Intake

Implement Rewrite webhook ingestion with security and reliability controls.

## Workflow

1. Register a webhook in the dashboard, API, or CLI for the required Rewrite event types.
2. Verify the request signature from the raw body plus `svix-*` headers before trusting the payload.
3. Parse the standard event envelope and normalize it to your internal event schema.
4. Deduplicate by Rewrite webhook event ID before side effects.
5. Persist the raw payload plus the normalized representation for replay/debugging.
6. Enqueue downstream work and return a fast `2xx` response.
7. Use delivery logs when troubleshooting missing or repeatedly failing deliveries.

## Handler Design Rules

- Keep webhook handler thin and idempotent.
- Use the raw request body for signature verification.
- Return within the 5 second webhook timeout budget.
- Expect at-least-once delivery and 5 total retry attempts on failure.
- Store raw payload and normalized event representation.
- Prevent duplicate processing with webhook-event dedupe keys.
- Treat Rewrite `contactId` as transport metadata that helps correlation, not as your app's canonical user identity.
- Do not model this as end-user inbound SMS unless your application provides that layer itself.

## Signature Verification (Inline Example)

```typescript
import { Webhook } from "svix";

const wh = new Webhook(process.env.REWRITE_WEBHOOK_SECRET!);
const headers = { "svix-id": req.headers["svix-id"], "svix-timestamp": req.headers["svix-timestamp"], "svix-signature": req.headers["svix-signature"] };
const payload = wh.verify(rawBody, headers); // throws on invalid signature
```

Always pass the **raw request body** (Buffer/string before JSON parsing) to `verify`.

## Resource Map

- SDK and package setup: `references/installation.md`
- Signature verification patterns: `references/webhook-verification.md`
- Webhook event envelope and normalization contract: `references/payload-contract.md`
- Intake and retry-safe processing patterns: `references/inbound-processing-patterns.md`
- Event routing and downstream actions: `references/reply-routing.md`
- Delivery log inspection: `references/delivery-logs.md`

## Output Contract

Produce a normalized object matching this shape:

```json
{
  "webhookEventId": "msg_2xRL...",
  "eventType": "message.delivered",
  "rewriteMessageId": "re_9kFm...",
  "projectId": "proj_abc123",
  "contact": { "address": "+15551234567", "contactId": "ct_xyz" },
  "status": "delivered",
  "deliveredAt": "2026-04-06T14:32:00Z",
  "rawPayloadRef": "s3://webhooks/msg_2xRL.json",
  "tenantContext": { "orgId": "org_42" }
}
```
