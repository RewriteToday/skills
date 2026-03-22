# Rewrite SDK, REST, And CLI Setup

Use one of the official integration paths below.

## Official Rewrite Repositories

- Node SDK: `https://github.com/rewritetoday/node`
- Go SDK: `https://github.com/rewritetoday/golang`
- CLI: `https://github.com/rewritetoday/cli`
- REST client: `https://github.com/rewritetoday/rest`
- Types package: `https://github.com/rewritetoday/types`
- Zod package: `https://github.com/rewritetoday/zod`
- Docs source: `https://github.com/rewritetoday/docs`

## Node SDK

```bash
npm install @rewritetoday/sdk
# or
pnpm add @rewritetoday/sdk
# or
bun install @rewritetoday/sdk
```

```ts
import { Rewrite } from '@rewritetoday/sdk';

const rewrite = new Rewrite(process.env.REWRITE_API_KEY!);
```

Use this as the default SDK path for outbound messaging.

## Go SDK

```bash
go get github.com/rewritetoday/golang
```

```go
package main

import (
	rewrite "github.com/rewritetoday/golang"
)

func newClient(secret string) (*rewrite.Client, error) {
	return rewrite.New(secret)
}
```

The public docs already show message-send examples for Go, but the current `main` GitHub repository visibly exposes `APIKeys`, `Templates`, and `Webhooks` on the client. Check the installed version before assuming `Messages` helpers are available; if not, use the public REST API from Go.

## Low-Level JS/TS Stack

```bash
npm install @rewritetoday/rest @rewritetoday/types @rewritetoday/zod zod
```

Use this stack when you need explicit HTTP control, route builders, or runtime validation at the boundary.

## CLI

```bash
go install github.com/rewritetoday/cli@latest
# or
brew install rewrite-cli
```

```bash
rewrite --version
rewrite login
```

The CLI is useful for local testing, webhook forwarding, quick sends, and inspection.

## Required Environment Variables

```bash
REWRITE_API_KEY=rw_...
REWRITE_WEBHOOK_SECRET=whsec_...
```

Keep Rewrite secrets server-side only and rotate them through your secret manager.
