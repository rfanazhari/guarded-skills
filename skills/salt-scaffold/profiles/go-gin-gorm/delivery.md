# Delivery files

DTO fields use `json:"camelCase"`. Handlers call `httpresponse.HandleDomainError` for use case errors. Bind failures use `400` and `INVALID_INPUT`.

## `pkg/httpresponse/response.go`

`HandleDomainError` maps with `errors.Is`:

| Sentinel | HTTP | Code |
| :--- | :--- | :--- |
| `ErrInvalidInput` | 400 | `INVALID_INPUT` |
| `ErrUnauthorized` | 401 | `UNAUTHORIZED` |
| `ErrForbidden` | 403 | `FORBIDDEN` |
| `ErrNotFound` | 404 | `RESOURCE_NOT_FOUND` |
| `ErrConflict` | 409 | `RESOURCE_ALREADY_EXISTS` |
| `ErrInvalidStateTransition` | 422 | `INVALID_STATE_TRANSITION` |
| anything else | 500 | `INTERNAL_SERVER_ERROR` |

The 500 body says an unexpected error occurred. It does not echo the underlying error text.

`Success` writes `success`, `data`, and `meta` when meta is non-nil. `SuccessMessage` writes `success` and `message`.

## `delivery/http/{{aggregate}}handler/dto.go`

```go
type Create{{Aggregate}}Request struct {
	Title       string `json:"title" binding:"required"`
	Description string `json:"description"`
}

type {{Aggregate}}Response struct {
	ID          string    `json:"id"`
	Title       string    `json:"title"`
	Description string    `json:"description"`
	Status      string    `json:"status"`
	CreatedAt   time.Time `json:"createdAt"`
	UpdatedAt   time.Time `json:"updatedAt"`
}

type PageMeta struct {
	Page  int   `json:"page"`
	Limit int   `json:"limit"`
	Total int64 `json:"total"`
}
```

`ToResponseList` uses `make([]{{Aggregate}}Response, 0, len(items))`.

## `delivery/http/{{aggregate}}handler/handler.go`

- `POST` binds JSON, calls `Create` with `c.Request.Context()`, returns `201` and the DTO.
- `GET /:id` returns `200` and one DTO.
- `GET` collection reads `page` and `limit` with defaults `1` and `10`, returns `200`, the list, and `PageMeta`.
- `PATCH /:id/activate` returns `200` and the DTO.
- `DELETE /:id` returns `200` and a message.

Every error from the use case goes to `HandleDomainError`.

## `delivery/http/{{aggregate}}handler/handler_test.go`

Use `httptest` and a stub that implements the use case interface. Cover:

- create success: HTTP 201, `success` true, `data.status` equal to `DRAFT`
- create bind failure: HTTP 400, `error.code` equal to `INVALID_INPUT`
- get missing: the stub returns `errs.ErrNotFound`, HTTP 404, `error.code` equal to `RESOURCE_NOT_FOUND`
- activate illegal: the stub returns `errs.ErrInvalidStateTransition`, HTTP 422, `error.code` equal to `INVALID_STATE_TRANSITION`

## Router

`GET /healthz` returns `{"status":"ok","timestamp":"<RFC3339 UTC>"}` with no envelope.

Under `/api/v1/{{collection}}` register `POST ""`, `GET ""`, `GET /:id`, `DELETE /:id`, and `PATCH /:id/activate`.

Install logger, recovery, and CORS middleware. Recovery writes the 500 envelope and aborts. It does not let the process exit.

## CORS

`CORS(origin string)` sets `Access-Control-Allow-Origin` to that origin.

- When `origin` is `*`, set `Access-Control-Allow-Credentials` to `false`.
- Otherwise set `Access-Control-Allow-Credentials` to `true`.

Default origin comes from `CORS_ORIGIN`, and the config default is `http://localhost:3000`. Answer `OPTIONS` with 204.
