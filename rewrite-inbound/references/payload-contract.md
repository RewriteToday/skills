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

### Example: `message.sent`

```json
{
  "id": "748395130237498911",
  "createdAt": "2026-03-19T18:42:13.000Z",
  "type": "message.sent",
  "data": {
    "id": "748395130237498500",
    "projectId": "748395130237498412",
    "contact": "Fernanda",
    "contactId": "748395130237498515",
    "to": "+5511999999999",
    "tags": [
      { "name": "use_case", "value": "otp_login" }
    ],
    "type": "SMS",
    "status": "SENT",
    "content": "Rewrite: your verification code is 123456",
    "country": "br",
    "analysis": {
      "characters": 41,
      "encoding": "gsm7",
      "segments": {
        "concat": 153,
        "count": 1,
        "reason": "fits",
        "single": 160
      }
    },
    "error": null,
    "deliveredAt": null,
    "scheduledAt": null,
    "templateId": null
  }
}
```

`contact` and `contactId` are optional transport metadata and may be absent when the event does not resolve to a stored Rewrite contact.

### Example: `sms.otp`

```json
{
  "id": "748395130237498910",
  "createdAt": "2026-03-19T18:42:11.000Z",
  "type": "sms.otp",
  "data": {
    "id": "748395130237498500",
    "projectId": "748395130237498412",
    "to": "+5511999999999",
    "tags": [],
    "type": "OTP",
    "status": "SENT",
    "content": "OTP prefix=\"Rewrite\" expiresIn=5 secure=true to=\"+5511999999999\"",
    "country": "br",
    "analysis": {
      "characters": 64,
      "encoding": "gsm7",
      "segments": {
        "concat": 153,
        "count": 1,
        "reason": "fits",
        "single": 160
      }
    },
    "error": null,
    "deliveredAt": null,
    "scheduledAt": null,
    "templateId": null,
    "otp": {
      "prefix": "Rewrite",
      "expiresIn": 5,
      "expiresAt": "2026-03-19T18:47:11.000Z"
    }
  }
}
```

## Batch Event Shape

For `message.batch`, `data` contains:

- `id`: batch ID
- `projectId`
- `ids`: accepted message IDs that reached `SENT`

### Example: `message.batch`

```json
{
  "id": "748395130237498912",
  "createdAt": "2026-03-19T18:42:25.000Z",
  "type": "message.batch",
  "data": {
    "id": "748395130237498700",
    "projectId": "748395130237498412",
    "ids": [
      "748395130237498500",
      "748395130237498501",
      "748395130237498502"
    ]
  }
}
```

Keep the raw webhook payload for signature verification, debugging, replay, and downstream normalization in your own application.
