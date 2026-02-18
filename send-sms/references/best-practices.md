# Production Best Practices

## Reliability

- Enforce idempotency on every send endpoint.
- Use bounded retries with jitter for 429 and 5xx classes.
- Add circuit breaking to avoid cascading failures.
- Use background workers for non-blocking delivery.

## Data Model

Store at minimum:

- Rewrite message ID
- Destination and sender
- Business context tags
- Current state and state transition timestamp
- Last error category and code

## Observability

- Attach request correlation IDs to every send.
- Emit structured logs for send attempts and webhook transitions.
- Monitor queue latency, delivery success rate, and retry exhaustion rate.
- Alert on abnormal spikes in opt-out or carrier rejection events.

## Compliance

- Keep explicit user consent records.
- Implement STOP/HELP behavior according to regional rules.
- Enforce quiet-hours policy where required.
- Avoid sensitive PII in SMS body unless strictly required.
