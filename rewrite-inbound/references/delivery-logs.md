# Delivery Logs

Use Rewrite delivery logs to inspect what was delivered to your webhook endpoint and how your endpoint responded.

## Relevant Endpoints

- `GET /webhooks/{id}/logs` lists delivery attempts for a specific webhook
- `GET /logs/{id}` fetches one exact delivery attempt with the delivered payload

## Practical Uses

- confirm whether Rewrite actually attempted delivery
- inspect the exact event payload sent to your endpoint
- correlate repeated retries with your response codes
- debug transport failures, app errors, or bad signatures

## Retry Context

- Rewrite retries failed deliveries up to 5 total attempts
- retries stop after a `2xx` response, the fifth failed attempt, or webhook deactivation/deletion
- webhook delivery is at-least-once, so your app still needs dedupe and idempotency

## Retention

- Starter/default: 3 days
- Pro: 14 days
- Business: 16 days
- Enterprise: 1 month
