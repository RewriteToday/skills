---
name: sms-best-practices
description: General SMS best-practices guide for Rewrite-based systems. Use when the agent needs to define SMS architecture, contacts and segments strategy, deliverability, templates, retries, observability, webhook handling, or rollout guardrails before or alongside implementation.
---

# SMS Best Practices With Rewrite

Use this skill when the user wants guidance, standards, or a checklist for building SMS flows with Rewrite.

If the task is implementation-heavy, pair this skill with:

- `send-sms/` for outbound send code, contacts, segments, and delivery lifecycle work
- `rewrite-inbound/` for Rewrite webhook intake and event processing
- `agent-sms-inbox/` for application-owned SMS agents

## Rewrite Reality Check

- Public Rewrite API v1 base URL: `https://api.rewritetoday.com/v1`
- Public audience resources live at `/contacts` and `/segments`
- Public sends currently target Brazilian `+55` numbers unless the project has international sending enabled
- Message sends accept `to` or `contact`
- `contact` resolves by contact ID or exact contact name
- Sending to `to` also ensures a Rewrite contact exists for that phone number
- Templates use `templateId` plus `variables`
- Write operations should carry `Idempotency-Key`
- Delivery is asynchronous and webhook-driven
- Segments are audience containers, not a direct broadcast endpoint
- `message.delivered` exists in the public contract but is currently WIP

## Default Guidance

1. Start with the simplest reliable flow: one send path, one webhook endpoint, one persistence model.
2. Treat the initial send response as acceptance, not final delivery.
3. Add idempotency before adding retries or concurrency.
4. Persist raw webhook payloads and deduplicate by webhook event ID.
5. Move heavy work off the webhook request path.
6. Use templates for repeated or localized content.
7. Add batch sends only after single-send observability is solid.

## Content And Deliverability

- Use valid `+55` E.164 destination numbers.
- Keep OTP and transactional content short.
- Put brand context near the start of the SMS body.
- Prefer explicit, low-ambiguity wording over marketing-heavy copy.
- Watch segmentation counts and encoding because they affect cost and delivery behavior.
- Use `tags` to encode stable business context like `flow`, `campaign`, or `use_case`.

## Audience Strategy

- Model reusable recipients as Rewrite contacts instead of duplicating recipient metadata across message payloads.
- Keep contact names unique per project if you plan to address contacts by name in `/messages`.
- Use segments for audience reuse and campaign assembly, then fan out the segment contacts through `/messages` or `/messages/batch`.
- Treat Rewrite contact tags and your app's business state separately unless you intentionally mirror them.

## Templates And Localization

- Prefer templates when the same flow is reused.
- Keep variable names stable across all template variants.
- Keep `content` as the default fallback on templates.
- Put locale variants in template `i18n`, not in the message-create body.
- Treat localization as a template concern, not an ad hoc string-building concern.

## Reliability And Retries

- Retry only `429` and transient `5xx`.
- Do not blindly retry `400 INVALID_JSON_BODY`.
- Treat `401 INVALID_OR_MISSING_API_KEY` as a configuration issue.
- Treat `403 PROJECT_SUBSCRIPTION_REQUIRED` and `PROJECT_BILLING_PAYMENT_REQUIRED` as operational or billing blocks.
- Respect `X-RateLimit-Retry-After`; the current platform returns it in milliseconds.

### Retry Workflow

1. **Check status** — only retry `429` or `5xx` responses.
2. **Read header** — parse `X-RateLimit-Retry-After` (ms) from the response.
3. **Wait** — use the header value, or exponential backoff (1 s, 2 s, 4 s) if absent.
4. **Retry** — resend with the same `Idempotency-Key`.
5. **Validate** — confirm `2xx`; stop after 3 failed attempts.
6. **Escalate** — log message ID + error code, alert ops or dead-letter the job.

## Webhooks

- Verify signatures with the raw body and `svix-id`, `svix-timestamp`, `svix-signature`.
- Return a fast `2xx` once the event is durably accepted by your system.
- Expect at-least-once delivery.
- Deduplicate by webhook event ID.
- Keep downstream writes idempotent.
- Use delivery logs when debugging retries, malformed responses, or missing events.

### Signature Verification Example

```ts
import { Webhook } from "svix";

export function verifyWebhook(rawBody: string, headers: Record<string, string>) {
  const wh = new Webhook(process.env.REWRITE_WEBHOOK_SECRET!);
  return wh.verify(rawBody, {
    "svix-id": headers["svix-id"],
    "svix-timestamp": headers["svix-timestamp"],
    "svix-signature": headers["svix-signature"],
  });
}
```

## Observability

Persist or emit at minimum:

- Rewrite message ID
- idempotency key
- destination number
- tags or business correlation IDs
- accepted timestamp
- message analysis: encoding, characters, segments
- webhook event history
- last known status and last error code

## Architecture Patterns

- Small apps: official SDK plus one database table plus one webhook worker
- Service-oriented stacks: `@rewritetoday/rest` plus `@rewritetoday/types` or `@rewritetoday/zod`
- High volume flows: queue-backed fan-out, controlled concurrency, dead-letter handling
- Audience-heavy flows: Rewrite contacts plus segments for recipient modeling, then queue-backed batch orchestration
- Critical OTP flows: isolate traffic, rate limit aggressively, and keep content minimal

## Batch Strategy

- Use `POST /messages/batch` only for bounded chunks
- Current public contract accepts 1 to 100 messages per batch request
- Resolve segment members first; there is no direct `/segments/:id/send` public route
- Treat `message.batch` as a summary event, not a replacement for per-message tracking
- If every batch item fails, no `message.batch` event is emitted

## Review Checklist

When using this skill, produce recommendations grouped by: content and deliverability risks, reliability and retry policy, webhook and event-processing safety, observability gaps, and rollout or scaling risks. Prefer concrete guardrails and tradeoffs over generic SMS advice.
