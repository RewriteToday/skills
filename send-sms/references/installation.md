# Rewrite SDK And Package Setup

Use one of the following integration paths.

## Canonical Rewrite Repositories

- Node SDK: `https://github.com/rewritetoday/node`
- Go SDK: `https://github.com/rewritetoday/golang`
- REST API: `https://github.com/rewritetoday/rest`
- Types package: `https://github.com/rewritetoday/types`

## Node SDK + Types

```bash
npm install @rewritejs/sdk @rewritejs/types
```

```ts
// Template setup; confirm exact client constructor in the current SDK version.
import { Rewrite } from "@rewritejs/sdk";
import type { APIPostSendMessageBody } from "@rewritejs/types";

const client = new Rewrite(process.env.REWRITE_API_KEY);

const payload: APIPostSendMessageBody = {
  to: "+15551234567",
  message: "Your verification code is 123456",
};
```

## Go SDK

```bash
go get github.com/rewritetoday/golang
```

```go
// Template setup; confirm exact package path and constructor in the current SDK version.
package main

import (
    "os"

    rewrite "github.com/rewritetoday/golang"
)

func newClient() *rewrite.Client {
    return rewrite.NewClient(os.Getenv("REWRITE_API_KEY"))
}
```

## REST API

Use REST for any runtime or service that does not use a native SDK.
Use endpoint shape and auth details from `https://github.com/rewritetoday/rest`.

```bash
curl -X POST "$REWRITE_BASE_URL/messages" \
  -H "Authorization: Bearer $REWRITE_API_KEY" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: 2c6af9f4-5c06-4f4a-9ede-c1f8d3a24f56" \
  -d '{
    "to": "+15551234567",
    "message": "Your verification code is 123456"
  }'
```

## Environment Variables

```bash
REWRITE_API_KEY=...
REWRITE_WEBHOOK_SECRET=...
```

Keep secrets in a dedicated secret manager and rotate keys periodically.
