# Webhook Intake Patterns

## Thin Webhook + Queue

Use this default architecture:

1. Verify signature from the raw body and `svix-*` headers.
2. Parse and validate the event envelope.
3. Save the raw payload and an event-receipt record.
4. Deduplicate by Rewrite webhook event ID.
5. Push normalized work to a queue.
6. Return `200 OK` immediately.

Benefits:

- Lower timeout risk
- Better retry control against the 5-attempt Rewrite schedule
- Cleaner failure isolation

## Dedupe Keys

Use at least one of:

- webhook event ID
- message ID plus event type
- batch ID plus event type for `message.batch`

Reject duplicates before running expensive logic.

## Retry-Aware Processing

- Rewrite considers any `2xx` response successful.
- Non-`2xx`, timeouts, and network failures are retried.
- The current timeout is 5 seconds.
- The current retry schedule is immediate, `+5s`, `+1m`, `+5m`, `+15m`.

## Normalization And Routing

Resolve events to:

- tenant or account
- workflow or campaign
- message or batch aggregate
- downstream handler: analytics, reconciliation, failure remediation, or agent input

Do not invent end-user inbound-message fields that are not present in the public Rewrite webhook contract.
