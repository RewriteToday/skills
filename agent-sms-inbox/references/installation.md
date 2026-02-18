# Agent Inbox Dependencies

Use the same Rewrite packages as outbound and inbound pipelines.

## Canonical Rewrite Repositories

- Node SDK: `https://github.com/rewritetoday/node`
- Go SDK: `https://github.com/rewritetoday/golang`
- REST API: `https://github.com/rewritetoday/rest`
- Types package: `https://github.com/rewritetoday/types`

## Recommended Runtime Stack

- Webhook API service (Node or Go)
- Queue/worker layer for asynchronous agent actions
- Policy service or policy module for action gating
- Durable audit log store for decisions and tool calls
