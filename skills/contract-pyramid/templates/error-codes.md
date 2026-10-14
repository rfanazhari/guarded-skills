<!-- destination: `.context/02-SHARED-CONTRACTS/error-codes.md` — do not copy this comment into the target -->

# Error codes

This is the only list of error codes for {{Service}}. HTTP mappers and the OpenAPI error schema quote these strings.

| HTTP | Code | When |
| :--- | :--- | :--- |
| 400 | `INVALID_INPUT` | Body or query failed validation. |
| 401 | `UNAUTHORIZED` | Credentials are missing or invalid. |
| 403 | `FORBIDDEN` | The caller is not allowed to perform the action. |
| 404 | `RESOURCE_NOT_FOUND` | The target does not exist. |
| 409 | `RESOURCE_ALREADY_EXISTS` | A uniqueness rule was violated. |
| 422 | `INVALID_STATE_TRANSITION` | The lifecycle forbids the requested change. |
| 500 | `INTERNAL_SERVER_ERROR` | An unexpected failure. |

A new code is a new row in this table before it appears in a handler or a test.
