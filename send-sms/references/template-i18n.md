# Template I18n

Localization in Rewrite happens at the template layer, not on the public message-create body.

## Rules

- `POST /messages` does not accept an `i18n` field.
- Create or update `i18n` on templates through `POST /templates` or `PATCH /templates/{id}`.
- Fetch stored locale variants with `withi18n=true`.
- Locale keys are lowercase ISO country codes such as `br`, `us`, or `mx`.
- Public traffic currently targets Brazilian numbers, so `br` is the practical locale today.

## Create A Localized Template

```bash
curl -X POST "https://api.rewritetoday.com/v1/templates" \
  -H "Authorization: Bearer rw_..." \
  -H "Content-Type: application/json" \
  -d '{
    "name": "otp_login",
    "description": "Login OTP SMS",
    "content": "Rewrite: seu codigo e {{code}}",
    "i18n": {
      "br": "Rewrite: seu codigo e {{code}}"
    },
    "variables": [
      { "name": "code", "fallback": "000000" }
    ]
  }'
```

## Send With A Template

```bash
curl -X POST "https://api.rewritetoday.com/v1/messages" \
  -H "Authorization: Bearer rw_..." \
  -H "Content-Type: application/json" \
  -d '{
    "to": "+5511999999999",
    "templateId": "748395130237498600",
    "variables": {
      "code": "123456"
    }
  }'
```

## Design Rules

- Keep variable names identical across every locale variant.
- Keep `content` as the default fallback text.
- Prefer templates over repeated inline content when the same flow is reused.
