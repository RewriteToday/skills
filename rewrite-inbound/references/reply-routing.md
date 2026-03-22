# Event Routing And Downstream Actions

Route Rewrite webhook events to stable internal handlers before any business action.

## Suggested Routing Layers

1. Audit and persistence for every accepted webhook event
2. Lifecycle updates for `message.queued`, `message.sent`, `message.scheduled`, `message.canceled`
3. Failure remediation for `message.failed`
4. Batch reconciliation for `message.batch`
5. OTP-specific handling for `sms.otp`
6. Optional application-owned agent or workflow triggers

## Routing Rules

- Drive business logic from webhook events, not only from the initial send response.
- Keep `message.delivered` optional until the event is generally available.
- Treat `message.batch` as a summary, not as proof that every request item succeeded.
- Route OTP success and OTP failure differently: success is `sms.otp`, failure is `message.failed`.

## Failure Strategy

- If routing fails after verification, store the raw payload and retry internally.
- Use Rewrite delivery logs to inspect what was actually delivered and returned by your endpoint.
- Escalate repeated handler failures to an operator queue before the webhook keeps failing every attempt.
