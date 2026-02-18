---
name: rewrite
description: Comprehensive skill router for Rewrite SMS workflows. Use when Codex needs to work with Rewrite for outbound SMS, inbound SMS/webhooks, delivery lifecycle, or AI-agent inbox processing with security controls.
---

# Rewrite

Use this skill as the entry point for Rewrite work and route to the focused sub-skill.

## Route To The Right Sub-skill

- Use `send-sms/` for outbound SMS sending, batch sends, retries, idempotency, and delivery workflows.
- Use `rewrite-inbound/` for inbound message webhooks, signature verification, payload handling, and reply flows.
- Use `agent-sms-inbox/` for AI-agent processing of inbound SMS with prompt-injection defenses and safe action policies.

## Shared Platform Checklist

1. Confirm API credentials and base URL for the target environment.
2. Confirm webhook signing secret and replay-window policy.
3. Confirm observability requirements: request IDs, message IDs, delivery events, and dead-letter handling.
4. Confirm SMS compliance requirements for the region (consent, opt-out, quiet hours, and retention).

## Canonical Rewrite Repositories

- Node SDK: `https://github.com/rewritetoday/node`
- Go SDK: `https://github.com/rewritetoday/golang`
- REST API: `https://github.com/rewritetoday/rest`
- Types package: `https://github.com/rewritetoday/types`

## Execution Policy

1. Start with the smallest viable implementation (single send + webhook receive).
2. Add idempotency, retries, and queue-backed processing before production rollout.
3. Add security hardening before enabling autonomous agent actions.
