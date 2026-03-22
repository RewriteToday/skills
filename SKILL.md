---
name: rewrite
description: Rewrite platform router for API, SDK, CLI, webhooks, templates, and AI workflows. Use when Codex needs to send SMS, handle Rewrite webhook events, work with official Rewrite SDKs/packages, or build application-owned agent flows on top of Rewrite.
---

# Rewrite

Use this skill as the entry point for Rewrite work and route to the focused sub-skill or package layer.

## Route To The Right Sub-skill

- Use `send-sms/` for `POST /messages`, `/messages/batch`, templates, idempotency, delivery tracking, and outbound SMS workflows.
- Use `rewrite-inbound/` for Rewrite webhook ingestion into your app: signature verification, event parsing, dedupe, retries, delivery logs, and downstream routing.
- Use `agent-sms-inbox/` for application-owned SMS agents that consume Rewrite events and send replies through Rewrite under strict policy controls.

## Platform Reality Check

- Public Rewrite API v1 base URL: `https://api.rewritetoday.com/v1`
- Auth: `Authorization: Bearer rw_...`
- Public sends currently target Brazilian `+55` numbers.
- Inline sends use `content`; template sends use `templateId` plus `variables`.
- Write operations should carry `Idempotency-Key`.
- Delivery state is asynchronous and arrives via webhooks; `message.delivered` is currently WIP.
- Cursor pagination uses top-level `cursor.persist`, `cursor.next`, and `cursor.prev`.

## Official Rewrite Stack

- Node SDK: `@rewritetoday/sdk`
- Go SDK: `github.com/rewritetoday/golang`
- REST transport: `@rewritetoday/rest`
- Shared contracts: `@rewritetoday/types`
- Runtime validation: `@rewritetoday/zod`
- CLI: `github.com/rewritetoday/cli`
- Docs/source of truth: `docs.rewritetoday.com` and `github.com/RewriteToday/docs`
- Examples: `github.com/RewriteToday/examples`

## Default Execution Policy

1. Prefer official SDKs for product code; drop to `@rewritetoday/rest` only when runtime control matters.
2. Pair transport with `@rewritetoday/types` or `@rewritetoday/zod` when you need compile-time or runtime validation.
3. Register webhooks early; do not block business logic on the initial send response.
4. Treat quota, rate limits, billing blocks, and webhook retries as first-class production concerns.
