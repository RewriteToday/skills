# Production Best Practices

## Delivery And Content

- Public sends currently target Brazilian `+55` numbers unless the project has international sending enabled.
- Prefer `contact` for reusable recipients and raw `to` for one-off or bootstrap sends.
- Sending with `to` still ensures a Rewrite contact exists for that phone number.
- Keep OTP and transactional content short to avoid unnecessary segmentation.
- Put brand context near the start of the message body.
- Prefer templates when the same flow is reused across sends.
- Keep template variable names stable across every locale variant.

## Audience Modeling

- Use `/contacts` for reusable recipients, stable names, channel metadata, and per-contact JSON `tags`.
- Use `/segments` and `/segments/:id/contacts` for audience grouping only; actual dispatch still goes through `/messages` or `/messages/batch`.
- Keep contact names unique per project if your app will target messages by contact name.
- Distinguish contact `tags` from message `tags`: contact tags are a JSON object, message tags are an array of `{ name, value }`.

## Reliability And Limits

- Enforce idempotency on every send endpoint.
- Retry only `429` and transient `5xx`.
- Read `X-RateLimit-Limit`, `X-RateLimit-Remaining`, and `X-RateLimit-Retry-After`.
- Treat `X-RateLimit-Retry-After` as milliseconds.
- Use queue-backed workers for non-blocking delivery and quota-friendly concurrency.
- Segment critical OTP traffic away from low-priority campaigns.

## Data Model

Store at minimum:

- Rewrite message ID
- request target reference: `to` or `contact`
- resolved Rewrite contact ID when available
- destination number
- business context tags
- idempotency key
- acceptance timestamp
- `analysis` payload: encoding, characters, segments
- Current state and state transition timestamp
- Last error category and code

## Observability

- Attach request correlation IDs to every send.
- Emit structured logs for send attempts and webhook transitions.
- Monitor queue latency, delivery success rate, retry exhaustion rate, and quota saturation.
- Persist webhook payloads and deduplicate by webhook event ID.
- Alert on abnormal spikes in `message.failed`, billing blocks, or quota exhaustion.

## Failure Handling

- Treat `400 INVALID_JSON_BODY` as a payload fix, not a retry.
- Treat `401 INVALID_OR_MISSING_API_KEY` as an auth/config issue.
- Treat `403 PROJECT_SUBSCRIPTION_REQUIRED` and `PROJECT_BILLING_PAYMENT_REQUIRED` as operational/commercial blocks.
- Inspect `message.failed` payloads for provider-facing error details.
- Do not wait on `message.delivered` for correctness-critical workflows; it is publicly marked WIP.
