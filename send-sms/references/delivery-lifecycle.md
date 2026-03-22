# Delivery Lifecycle

Treat delivery as an asynchronous state machine driven by webhooks.

## Immediate SMS

Typical flow:

1. `POST /messages` returns an accepted Rewrite message ID immediately.
2. `message.queued` is emitted once Rewrite persists the immediate SMS for dispatch.
3. `message.sent` is emitted when the provider accepts the SMS.
4. `message.failed` is emitted if Rewrite cannot complete the send.
5. `message.delivered` exists in the public contract but is currently WIP.

## Scheduled SMS

Typical flow:

1. Send with `scheduledAt`.
2. `message.scheduled` is emitted after acceptance.
3. Later lifecycle events can be `message.sent`, `message.failed`, or `message.canceled`.

## Batch SMS

- `POST /messages/batch` accepts 1 to 100 messages.
- Individual message lifecycle events still happen per stored message.
- `message.batch` is emitted only when no batch messages remain pending and at least one stored message reached `SENT`.
- If every batch item fails, no `message.batch` event is emitted.

## OTP Events

- Successful OTP sends emit `sms.otp`.
- OTP failures emit `message.failed`, not a separate failure event.

## Lifecycle Rules

1. Never assume finality from the initial send response.
2. Persist the accepted ID and correlate later webhook events with it.
3. Allow out-of-order webhook events and keep deterministic internal transitions.
4. Make webhook handlers idempotent and preserve the full event history.
5. Use `GET /messages/{id}` for point-in-time inspection, not as a replacement for webhook processing.
