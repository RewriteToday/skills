# Webhook Verification

Validate authenticity before trusting Rewrite event payloads.

## Verification Steps

1. Read the raw request body before JSON parsing.
2. Extract `svix-id`, `svix-timestamp`, and `svix-signature`.
3. Verify the signature against the webhook secret returned by Rewrite.
4. Reject missing or invalid signatures before any side effects.
5. Parse the JSON only after signature verification succeeds.
6. Validate the parsed envelope shape before routing the event.

## Node Pattern

```ts
import { Rewrite } from '@rewritetoday/sdk';
import { WebhookEvent } from '@rewritetoday/zod/v1';

const rewrite = new Rewrite(process.env.REWRITE_API_KEY!);

export async function handleRewriteWebhook(request: Request) {
  const payload = await request.text();

  const isValid = rewrite.webhooks.verify({
    payload,
    secret: process.env.REWRITE_WEBHOOK_SECRET,
    headers: {
      'svix-id': request.headers.get('svix-id') ?? undefined,
      'svix-timestamp': request.headers.get('svix-timestamp') ?? undefined,
      'svix-signature': request.headers.get('svix-signature') ?? undefined,
    },
  });

  if (!isValid) {
    return new Response('invalid signature', { status: 401 });
  }

  const parsed = WebhookEvent.safeParse(JSON.parse(payload));

  if (!parsed.success) {
    return new Response('invalid payload', { status: 400 });
  }

  return Response.json({ ok: true });
}
```

## Secret Format Notes

- Rewrite accepts `whsec_<base64>`, raw base64, or legacy `rw_whsec_...` secret formats.
- Use the exact secret returned by `POST /webhooks` or `GET /webhooks/{id}`.
- Keep the webhook body raw until verification is complete.
