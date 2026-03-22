# Agent Inbox Dependencies

Build the agent on top of the official Rewrite transport/event layer plus your own application state.

## Official Rewrite Repositories

- Node SDK: `https://github.com/rewritetoday/node`
- Go SDK: `https://github.com/rewritetoday/golang`
- CLI: `https://github.com/rewritetoday/cli`
- REST API: `https://github.com/rewritetoday/rest`
- Types package: `https://github.com/rewritetoday/types`
- Zod package: `https://github.com/rewritetoday/zod`

## Recommended Runtime Stack

- Webhook API service for Rewrite events
- Queue or worker layer for asynchronous agent actions
- Policy service or policy module for action gating
- Durable audit log store for decisions and tool calls
- Application-owned conversation and identity store

## Boundary Rule

Rewrite is the transport and webhook layer in this design. The inbox state, user identity, confirmation flow, and tool policies belong to your application.
