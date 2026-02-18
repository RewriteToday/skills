---
name: rewrite-inbound
description: Inbound SMS processing workflow for Rewrite. Use when Codex needs to configure inbound webhooks, verify signatures, parse and normalize inbound payloads, handle media, correlate conversations, and generate replies safely.
---

# Rewrite Inbound SMS

Implement inbound message handling with security and reliability controls.

## Workflow

1. Configure Rewrite inbound webhook endpoint for your environment.
2. Verify webhook signature and replay window before processing payloads.
3. Normalize inbound payload to your internal message schema.
4. Correlate sender and conversation context.
5. Enqueue downstream processing and return fast success response.
6. Send reply asynchronously using the outbound skill (`send-sms/`).

## Handler Design Rules

- Keep webhook handler thin and idempotent.
- Reject unsigned or stale requests.
- Store raw payload and normalized event representation.
- Prevent duplicate processing with event/message dedupe keys.

## Resource Map

- SDK and package setup: `references/installation.md`
- Signature verification patterns: `references/webhook-verification.md`
- Inbound processing pipeline: `references/inbound-processing-patterns.md`
- Reply and routing strategies: `references/reply-routing.md`
- Event contract template: `references/payload-contract.md`

## Output Contract

Produce a normalized object with:

- provider event ID
- rewrite message ID
- sender number
- recipient number
- message text
- media metadata
- received timestamp
- tenant/application context
