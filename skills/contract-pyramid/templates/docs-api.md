<!-- destination: `docs/03-api-spec.md` — do not copy this comment into the target -->

# HTTP API

JSON object keys on the wire are camelCase.

## Success

```json
{
  "success": true,
  "data": {},
  "meta": { "page": 1, "limit": 10, "total": 0 }
}
```

`meta` is present on list responses. An empty list is `[]`.

## Error

```json
{
  "success": false,
  "error": { "code": "RESOURCE_NOT_FOUND", "message": "...", "details": null }
}
```

`code` is a string from `.context/02-SHARED-CONTRACTS/error-codes.md`.

## Health

`GET /healthz` returns `{"status":"ok","timestamp":"<ISO-8601>"}`. It does not use the envelope.

## Routes

Authoritative paths live in `.context/02-SHARED-CONTRACTS/openapi-spec.yaml`.

| Method | Path | Success |
| :--- | :--- | :--- |
| GET | `/healthz` | 200, raw health body |
| POST | `/api/v1/{{collection}}` | 201, one `{{Aggregate}}` |
| GET | `/api/v1/{{collection}}` | 200, a page and `meta` |
| GET | `/api/v1/{{collection}}/:id` | 200, one `{{Aggregate}}` |
| DELETE | `/api/v1/{{collection}}/:id` | 200, a message |
| PATCH | `/api/v1/{{collection}}/:id/activate` | 200, one `{{Aggregate}}` |

List defaults: `page` below 1 becomes 1. `limit` outside 1..100 becomes 10.

Create body: `title` required, 1..255 characters after trim. `description` optional.
