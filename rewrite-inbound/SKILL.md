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
- Official webhook event envelope and payload shapes: `references/payload-contract.md`
- Intake and retry-safe processing patterns: `references/inbound-processing-patterns.md`
- Event routing and downstream actions: `references/reply-routing.md`
- Delivery log inspection: `references/delivery-logs.md`

## Output Contract

Expect the official Rewrite webhook delivery envelope documented in `references/payload-contract.md`:

```json
{
  "id": "748395130237498911",
  "createdAt": "2026-03-19T18:42:13.000Z",
  "type": "message.sent",
  "data": {
    "id": "748395130237498500",
    "projectId": "748395130237498412",
    "contact": "Fernanda",
    "contactId": "748395130237498515",
    "to": "+5511999999999",
    "tags": [
      { "name": "use_case", "value": "otp_login" }
    ],
    "type": "SMS",
    "status": "SENT",
    "content": "Rewrite: your verification code is 123456",
    "country": "br",
    "analysis": {
      "characters": 41,
      "encoding": "gsm7",
      "segments": {
        "concat": 153,
        "count": 1,
        "reason": "fits",
        "single": 160
      }
    },
    "error": null,
    "deliveredAt": null,
    "scheduledAt": null,
    "templateId": null
  }
}
```

If your app needs a normalized internal schema, derive it from this envelope after signature verification and raw payload persistence.
