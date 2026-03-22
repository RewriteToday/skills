# Rewrite Webhook Intake Setup

Reuse the official Rewrite packages that cover webhooks, logs, contracts, and validation.

## Official Rewrite Repositories

- Node SDK: `https://github.com/rewritetoday/node`
- Go SDK: `https://github.com/rewritetoday/golang`
- CLI: `https://github.com/rewritetoday/cli`
- REST transport: `https://github.com/rewritetoday/rest`
- Types package: `https://github.com/rewritetoday/types`
- Zod schemas: `https://github.com/rewritetoday/zod`

## Recommended JS/TS Stack

```bash
npm install @rewritetoday/sdk @rewritetoday/zod zod
```

Use `@rewritetoday/sdk` for webhook creation, log access, and signature verification, then use `@rewritetoday/zod` to validate the parsed event payload.

## CLI For Local Forwarding

```bash
rewrite login
rewrite listen --forward-to http://localhost:3000/webhooks/rewrite
```

Use this for local development when you want Rewrite events forwarded into your local server.

## Go Stack

```bash
go get github.com/rewritetoday/golang
```

The current public Go repository visibly exposes webhook, template, and API key resources. If you need richer webhook tooling or runtime validation today, pair Go with the public REST/webhook contracts from the docs or use the Node SDK utilities at the edge.

## Registration Rules

- Configure the webhook URL in the dashboard or through `POST /webhooks`.
- Production endpoints must be HTTPS.
- `http://localhost/...` is accepted for local development.
- Keep `REWRITE_WEBHOOK_SECRET` in your secret manager.

## Required Environment Variables

```bash
REWRITE_API_KEY=rw_...
REWRITE_WEBHOOK_SECRET=whsec_...
```
