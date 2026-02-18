# Batch SMS And Queue Patterns

Use batch flows when the same template or campaign must reach many recipients.

## Preferred Production Pattern

1. Store campaign metadata and target set in your database.
2. Chunk recipients to bounded jobs.
3. Push jobs to a queue with retry and dead-letter support.
4. Send each message with per-recipient idempotency keys.
5. Track per-message state from send response and webhooks.

## Node Worker Example

```ts
export async function processCampaignChunk(campaignId: string, recipients: string[]) {
  for (const to of recipients) {
    const idempotencyKey = `campaign:${campaignId}:${to}`;

    try {
      const result = await client.messages.send(
        {
          to,
          idempotencyKey,
          message: "Flash sale: 20% off today only",
          tags: { campaignId },
        },
      );

      await saveDeliveryRecord({
        campaignId,
        to,
        messageId: result.id,
        status: result.status,
      });
    } catch (err) {
      await registerFailure(campaignId, to, err);
      
      throw err;
    }
  }
}
```

## Failure Policy

- Retry transient failures with exponential backoff and jitter.
- Stop retrying permanent validation failures.
- Route exhausted retries to dead-letter handling.
- Surface campaign health metrics in real time.
