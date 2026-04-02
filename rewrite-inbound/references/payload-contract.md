# Rewrite Webhook Event Contract

Every public Rewrite webhook event uses the same top-level envelope:

```json
{
  "id": "748395130237498900",
  "createdAt": "2026-03-19T18:42:11.000Z",
  "type": "message.sent",
  "data": {}
}
```

## Declared Event Types

- `sms.otp`
- `message.sent`
- `message.batch`
- `message.queued`
- `message.failed`
- `message.canceled`
- `message.scheduled`
- `message.delivered` (currently WIP)

## Message-Like Event Shape

For `sms.otp`, `message.sent`, `message.queued`, `message.failed`, `message.canceled`, `message.scheduled`, and `message.delivered`, `data` contains a message-shaped payload with fields such as:

- `id`
- `projectId`
- `to`
- `contact`
- `contactId`
- `tags`
- `type`
- `status`
- `content`
- `country`
- `analysis`
- `error`
- `deliveredAt`
- `scheduledAt`
- `templateId`
- `otp` only on `sms.otp`

## Batch Event Shape

For `message.batch`, `data` contains:

- `id`: batch ID
- `projectId`
- `ids`: accepted message IDs that reached `SENT`

## Suggested Internal Normalization

```json
{
  "eventId": "748395130237498900",
  "eventType": "message.sent",
  "createdAt": "2026-03-19T18:42:11.000Z",
  "projectId": "748395130237498412",
  "messageId": "748395130237498500",
  "batchId": null,
  "status": "SENT",
  "to": "+5511999999999",
  "contact": "Ada",
  "contactId": "748395130237498455",
  "tags": [
    { "name": "flow", "value": "login" }
  ],
  "analysis": {
    "encoding": "gsm7",
    "characters": 41,
    "segments": {
      "count": 1,
      "single": 160,
      "concat": 153,
      "reason": "fits"
    }
  },
  "raw": {}
}
```

`contact` and `contactId` are nullable and help correlate delivery events with Rewrite audience records.

Keep both normalized and raw payloads for troubleshooting, replay, and log correlation.
