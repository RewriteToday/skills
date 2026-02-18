# Inbound Processing Patterns

## Thin Webhook + Queue

Use this default architecture:

1. Verify signature.
2. Parse and minimally validate payload.
3. Save event receipt record.
4. Push normalized job to queue.
5. Return `200 OK` immediately.

Benefits:

- Lower timeout risk
- Better retry control
- Cleaner failure isolation

## Dedupe Keys

Use at least one of:

- provider event ID
- message ID + event type
- sender + timestamp + body hash

Reject duplicates before running expensive logic.

## Media Handling

- Do not download media inside webhook response path.
- Queue media fetch jobs with size/type checks.
- Sanitize file names and scan attachments if required.

## Conversation Correlation

Resolve inbound messages to:

- tenant/account
- end-user profile
- active workflow/conversation
- escalation state (human handoff, blocked user, high risk)
