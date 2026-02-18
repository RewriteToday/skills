# Single SMS Examples

Use these examples as implementation templates.

## Node SDK Pattern

```ts
import { Rewrite } from "@rewritejs/sdk";

const client = new Rewrite(process.env.REWRITE_API_KEY);

export async function sendVerificationSms(to: string, code: string) {
  const idempotencyKey = `verify:${to}:${code}`;

  const result = await client.messages.send(
    {
      to,
      idempotencyKey,
      message: `Your code is ${code}`,
      tags: { purpose: "verification" },
    },
  );

  return {
    messageId: result.id,
    providerState: result.status,
  };
}
```

## Go SDK Pattern

```go
func SendVerificationSMS(client *rewrite.Client, to string, code string) (*rewrite.Message, error) {
    idempotencyKey := fmt.Sprintf("verify:%s:%s", to, code)

    msg, err := client.Messages.Send(context.Background(), rewrite.SendMessageRequest{
        To:   to,
        IdempotencyKey: idempotencyKey,,
        Text: fmt.Sprintf("Your code is %s", code),
        Tags: map[string]string{"purpose": "verification"},
    })
    
    if err != nil {
        return nil, err
    }

    return msg, nil
}
```

## REST Pattern

```bash
curl -X POST "$REWRITE_BASE_URL/messages" \
  -H "Authorization: Bearer $REWRITE_API_KEY" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: verify:+15551234567:123456" \
  -d '{
    "to": "+15551234567",
    "message": "Your code is 123456",
    "tags": { "purpose": "verification" }
  }'
```

If your SDK version uses different method names, keep the same call structure and semantics: payload + idempotency + response mapping.
