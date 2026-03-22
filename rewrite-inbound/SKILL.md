---
name: rewrite-inbound
description: Rewrite webhook ingestion workflow for your application. Use when Codex needs to configure Rewrite webhooks, verify signatures, parse and normalize event payloads, handle retries and dedupe, inspect delivery logs, and route delivery or OTP events safely.
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
- Do not model this as end-user inbound SMS unless your application provides that layer itself.

## Resource Map

- SDK and package setup: `references/installation.md`
- Signature verification patterns: `references/webhook-verification.md`
- Webhook event envelope and normalization contract: `references/payload-contract.md`
- Intake and retry-safe processing patterns: `references/inbound-processing-patterns.md`
- Event routing and downstream actions: `references/reply-routing.md`
- Delivery log inspection: `references/delivery-logs.md`

## Output Contract

Produce a normalized object with:

- webhook event ID
- event type
- Rewrite message ID or batch ID
- project ID
- current message status, when applicable
- acceptance or delivery timestamps, when applicable
- raw payload storage location
- tenant or application correlation context
