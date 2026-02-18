# Normalized Payload Contract

Use this internal contract after provider payload parsing.

```json
{
	"id": "12345678901230921"
	"type": "sms.queued",
	"data": {
		"to": "+15557654321",
		"from": "+15551234567",
		"createdAt": "2026-02-18T10:00:00Z",
		"message": "Hey, your OTP code is 123213",
		"variables": [
			{
				"name": "code",
				"value": "123213"
			},
		],
		"attachments": [
			{
				"id": "12312312321312312",
				"type": "image/jpeg",
				"url": "https://..."
			}
		],
		"metadata": {
			"user": {
				"email": "user@example.com"
			}
		}
	},
}
```

Keep both normalized and raw payloads for troubleshooting and replay.
