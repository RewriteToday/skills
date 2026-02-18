# Inbound Integration Setup

Reuse the same Rewrite packages used for outbound messaging.

## Canonical Rewrite Repositories

- Node SDK: `https://github.com/rewritetoday/node`
- Go SDK: `https://github.com/rewritetoday/golang`
- REST API: `https://github.com/rewritetoday/rest`
- Types package: `https://github.com/rewritetoday/types`

## Node Stack

```bash
npm install @rewritejs/sdk @rewritejs/types
```

## Go Stack

```bash
go get github.com/rewritetoday/golang
```

## REST/Webhook Stack

- Configure webhook URL in Rewrite dashboard or API configuration.
- Keep `REWRITE_WEBHOOK_SECRET` in your secret manager.
- Route webhook traffic through HTTPS only.

## Required Environment Variables

```bash
REWRITE_API_KEY=...
REWRITE_WEBHOOK_SECRET=...
```
