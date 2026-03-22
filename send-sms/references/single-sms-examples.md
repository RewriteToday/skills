# Single SMS Examples

Use these examples as implementation templates.

## Node SDK Pattern

```ts
import { Rewrite } from '@rewritetoday/sdk';

const rewrite = new Rewrite(process.env.REWRITE_API_KEY!);

export async function sendVerificationSms(to: string, code: string) {
	const { data, error } = await rewrite.messages.send({
		to,
		content: `Rewrite: your verification code is ${code}`,
		tags: [{ name: 'flow', value: 'login' }],
		idempotencyKey: `msg:verify:${to}:${code}`,
	});

	if (error) throw error;

	return data;
}
```

The accepted response carries the Rewrite message ID and `analysis` payload; persist both.

## Template Send Pattern

```ts
const { data, error } = await rewrite.messages.send({
  to: '+5511888888888',
  templateId: '748395130237498620',
  variables: {
    code: '941205',
    name: 'Fernanda',
  },
  tags: [{ name: 'flow', value: 'login' }],
  idempotencyKey: 'msg:template:login:941205',
});

if (error) throw error;
```

## REST Pattern

```bash
curl -X POST "https://api.rewritetoday.com/v1/messages" \
  -H "Authorization: Bearer rw_..." \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: msg:verify:+5511999999999:123456" \
  -d '{
    "to": "+5511999999999",
    "content": "Rewrite: your verification code is 123456",
    "tags": [
      { "name": "flow", "value": "login" }
    ]
  }'
```

## Go Note

The public docs already show `Messages.Send(...)` examples for Go, but the public `main` repository currently does not expose a `Messages` resource in the visible client source. If your installed Go SDK version does not provide message helpers yet, call the same public REST contract from Go and keep the request body identical to the examples above.
