# Contacts And Segments

Use these project-scoped audience resources when the SMS flow needs reusable recipients instead of ad hoc phone numbers.

All routes below live under `https://api.rewritetoday.com/v1` and rely on the active project auth context. The project ID is not part of the path.

If your installed SDK version does not expose `contacts` or `segments` helpers yet, call these routes through the public REST API with the same request bodies documented here.

## Contacts Contract

- `POST /contacts`
  Create a contact with `phone`, optional `name`, optional `channel`, and optional JSON-object `tags`.
- `GET /contacts?limit=...&after=...&before=...`
  List contacts with cursor pagination. Max `limit` is `100`.
- `GET /contacts/:identifier`
  Fetch one contact by numeric contact ID or exact contact name.
- `PATCH /contacts/:id`
  Partially update the same fields accepted on create.
- `DELETE /contacts/:id`
  Remove the contact from the project.

### Contact Notes

- Contact names are unique per project. If you target messages by `contact` name, use stable exact names.
- Contact phone numbers are unique per project.
- Contact `tags` are a JSON object map, not the message tag array format.
- `channel` uses the Rewrite message-type enum and is optional metadata on the contact record.

## Message Targeting With Contacts

`POST /messages` and `POST /messages/batch` accept:

- `to`: a raw E.164 phone number
- `contact`: a Rewrite contact ID or exact contact name

When you send with raw `to`, Rewrite still ensures a contact exists for that phone number inside the project. That makes later audience reuse and webhook correlation easier, but you should still create or enrich contacts explicitly when you need stable names, channel metadata, or contact tags.

## Segments Contract

- `POST /segments`
  Create a segment with required `name`, optional `color`, and optional `description`.
- `GET /segments?limit=...&after=...&before=...`
  List segments with cursor pagination. Max `limit` is `50`.
- `GET /segments/:id`
  Fetch one segment.
- `PATCH /segments/:id`
  Partially update the same fields accepted on create.
- `DELETE /segments/:id`
  Delete the segment.
- `GET /segments/:id/contacts?limit=...&after=...&before=...`
  List the contacts attached to the segment. Max `limit` is `100`.
- `POST /segments/:id/contacts`
  Attach one contact to the segment with `{ "contactId": "..." }`.
- `DELETE /segments/:id/contacts/:contact`
  Detach one contact from the segment.

### Segment Notes

- Segment names are unique per project.
- Segment `color` is normalized to uppercase `#RRGGBB`.
- Segment responses include `contactsCount`.
- Segments are audience containers only. There is no direct public route like `/segments/:id/send`.

## Recommended Audience Workflow

1. Create or sync recipients into `/contacts`.
2. Create stable campaign or lifecycle groups in `/segments`.
3. Attach contacts to those segments as your business state changes.
4. Resolve the current audience with `GET /segments/:id/contacts`.
5. Fan out the returned contact IDs into `POST /messages` or `POST /messages/batch`.

## REST Example

```bash
curl -X POST "https://api.rewritetoday.com/v1/contacts" \
  -H "Authorization: Bearer rw_..." \
  -H "Content-Type: application/json" \
  -d '{
    "phone": "+5511999999999",
    "name": "Ada",
    "channel": "SMS",
    "tags": {
      "crm_id": "cus_123",
      "plan": "pro"
    }
  }'
```

```bash
curl -X POST "https://api.rewritetoday.com/v1/segments/748395130237498455/contacts" \
  -H "Authorization: Bearer rw_..." \
  -H "Content-Type: application/json" \
  -d '{
    "contactId": "748395130237498512"
  }'
```

```bash
curl "https://api.rewritetoday.com/v1/segments/748395130237498455/contacts?limit=100" \
  -H "Authorization: Bearer rw_..."
```
