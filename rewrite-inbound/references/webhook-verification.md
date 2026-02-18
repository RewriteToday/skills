# Webhook Verification

Validate authenticity before reading inbound message content.

## Verification Steps

1. Read raw request body bytes.
2. Extract Rewrite signature header and timestamp header.
3. Recompute expected signature with `REWRITE_WEBHOOK_SECRET`.
4. Compare signatures in constant time.
5. Enforce replay window (for example, 5 minutes).
6. Reject invalid requests with 401/403.

## Node Pattern

```ts
import { Rewrite } from "@rewritejs/sdk";

const rewrite = new Rewrite(process.env.REWRITE_API_KEY);

function verifyRewriteWebhook({
  rawBody,
  signature,
}: {
  rawBody: Buffer;
  signature: string;
}) {
  return rewrite.webhooks.verifySignature(rawBody, signature);
}
```

Adjust header names and canonicalization format to the exact Rewrite webhook spec.
