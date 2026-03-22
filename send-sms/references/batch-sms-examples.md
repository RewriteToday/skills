# Batch SMS And Queue Patterns

Use batch flows when one request needs to accept many outbound SMS messages at once.

## Contract Constraints

- `POST /messages/batch` accepts between 1 and 100 items.
- Each item uses the same body shape as a single message.
- Attach `Idempotency-Key` at the request level.
- The accepted response returns only the accepted Rewrite message IDs.

## Preferred Production Pattern

1. Store campaign metadata and target set in your database.
2. Chunk recipients to bounded jobs.
3. Push jobs to a queue with retry and dead-letter support.
4. Use batch requests only for bounded chunks that still fit the 1..100 contract.
5. Track per-message state from accepted IDs plus webhook events.

## Node Worker Example

```ts
import { Rewrite } from '@rewritetoday/sdk';

const rewrite = new Rewrite(process.env.REWRITE_API_KEY!);

export async function processCampaignChunk(campaignId: string, recipients: string[]) {
	const body = recipients.map((to) => ({
		to,
		content: 'Rewrite: flash sale, 20% off today only',
		tags: [{ name: 'campaign', value: campaignId }],
	}));

	const { data, error } = await rewrite.messages.batch(body, {
		idempotencyKey: `msg:batch:${campaignId}`,
	});

	if (error) throw error;

	return data;
}
```

## REST Example

```bash
curl -X POST "https://api.rewritetoday.com/v1/messages/batch" \
  -H "Authorization: Bearer rw_..." \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: msg:batch:campaign-42" \
  -d '[
    {
      "to": "+5511999999999",
      "content": "Rewrite: seu codigo e 478201"
    },
    {
      "to": "+5511888888888",
      "templateId": "748395130237498620",
      "variables": { "code": "941205" }
    }
  ]'
```

## Failure Policy

- Retry transient failures with exponential backoff and jitter.
- Stop retrying permanent validation failures.
- Route exhausted retries to dead-letter handling.
- Treat `message.batch` as a summary event only; individual message events still matter.
- If every batch item fails, no `message.batch` event is emitted.
