# Delivery Lifecycle

Treat delivery as a state machine.

## Suggested States

- `queued`
- `delivered`
- `failed`
- `queued`
- `canceled`

Map Rewrite event payloads to your internal states, then trigger domain actions from stable internal events only.

## Lifecycle Rules

1. Never assume finality from the initial send response.
2. Allow out-of-order webhook events and keep last-write-wins logic by timestamp/version.
3. Make webhook handlers idempotent.
4. Preserve full event payload history for diagnostics.

## Retry Triggers

- Retry app-level failures in outbound worker.
- Do not retry webhook events by re-sending SMS.
- Retry webhook processing internally if downstream persistence fails.
