<div align="center">

# Rewrite Skills

A set of specialized knowledge, rules, and tools to accelerate **Rewrite** integrations through **AI agents** and SMS automation.

<img src="https://cdn.rewritetoday.com/assets/banners/ai.png" width="100%" alt="Rewrite"/>

## What are SKILLS?

Skills are specialized context packages that teach **AI agents** (such as **Cursor**, **Claude Code**, and other assistants) and developers about best practices, business rules, and technical standards in the Rewrite ecosystem.

## What are they for?

<strong>Precise Context:</strong> Provide practical information about Rewrite APIs, SDKs, Webhooks, CLI, and ecosystem packages.<br/>
<strong>Standardization:</strong> Ensure generated code follows consistent conventions for SMS sending, contacts and segments, webhook intake, and agent-safe workflows.<br/>
<strong>Acceleration:</strong> Reduce implementation time with production-ready workflows and checklists.<br/>
<strong>Practical Examples:</strong> Offer clear reference patterns for Node.js, Go, REST, Types, and Zod.

## How to Install

These skills are primarily consumed by **IDEs** and **AI agents** that support local context ingestion.

1. **Clone:** Clone this repository to your machine.<br/>
2. **AI Context:** Add this skills folder to your editor/agent context.<br/>
3. **CLI:** Also use the [Rewrite CLI](https://github.com/rewritetoday/cli) for operational flows, webhook forwarding, and local automation.

## How to Use

The structure is organized by domain for easier discovery and execution:

[**`SKILL.md`**](./SKILL.md) — Main Rewrite skill router.<br/>
[**`sms-best-practices/`**](./sms-best-practices) — General SMS architecture, deliverability, retry, observability, and rollout guidance for Rewrite-based systems.<br/>
[**`send-sms/`**](./send-sms) — Outbound sending, contacts, segments, idempotency, templates, batching, and delivery lifecycle.<br/>
[**`rewrite-inbound/`**](./rewrite-inbound) — Rewrite webhook intake, signature verification, payload normalization, and delivery-log debugging.<br/>
[**`agent-sms-inbox/`**](./agent-sms-inbox) — Secure application-owned SMS agent patterns with anti prompt-injection policies.

## Ecosystem

[Rewrite CLI](https://github.com/rewritetoday/cli) — Command-line tooling for operations and automation.<br/>
[Node SDK](https://github.com/rewritetoday/node) — JavaScript/TypeScript integration.<br/>
[Go SDK](https://github.com/rewritetoday/golang) — Native integration for Go services.<br/>
[REST API](https://github.com/rewritetoday/rest) — HTTP endpoint and contract reference.<br/>
[Types](https://github.com/rewritetoday/types) — Shared contracts and route builders.<br/>
[Zod](https://github.com/rewritetoday/zod) — Runtime validation for Rewrite payloads and webhooks.<br/>
[Docs](https://docs.rewritetoday.com) — Official API, SDK, CLI, Webhooks, AI, and Ecosystem documentation.<br/>
[Examples](https://github.com/rewritetoday/examples) — Official examples repository.

Built to accelerate production-grade SMS integrations against the current public Rewrite platform.<br/>
Truly open source.

</div>
