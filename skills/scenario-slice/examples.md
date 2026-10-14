# Example

The Ledger service from `salt-scaffold` is already running. Entries are created in `DRAFT` and activated to `ACTIVE`. Delete is a hard delete.

The user says: "scenario slice: archive an ACTIVE entry. It becomes ARCHIVED and cannot be activated. Archiving a DRAFT is rejected."

## Contract first

`contract-pyramid` adjust mode writes:

- `SC-09` archive of `ACTIVE` returns `200` and status `ARCHIVED`
- `SC-10` archive of `DRAFT` returns `422` and `INVALID_STATE_TRANSITION`
- a behavioral-contract row `ACTIVE` to `ARCHIVED`
- a note that `ARCHIVED` has no outbound transition
- `PATCH /api/v1/entries/{id}/archive` in the OpenAPI document

`SC-01` through `SC-08` stay as they are. No new error code is required.

## Code

| File | Change |
| :--- | :--- |
| `domain/enum/status.go` | `StatusArchived` |
| `domain/entity/entry.go` | `Archive(now)` allows only `ACTIVE` |
| `domain/entity/entry.go` | `Activate` still allows only `DRAFT`, so an archived entry fails activate |
| `application/usecase/entry/entry_usecase.go` | `Archive` loads, calls `Archive`, updates |
| `application/usecase/entry/entry_usecase_test.go` | `SC-09` and `SC-10` with a fixed clock |
| `delivery/http/entryhandler/handler.go` | `PATCH /:id/archive` |
| `delivery/http/router.go` | route registration |
| `delivery/http/entryhandler/handler_test.go` | 200 for archive, 422 when the stub returns `ErrInvalidStateTransition` |

The repository port does not change. `Update` already persists the new status. Conformance cases 1–3 stay as they are. No fourth case is added.

## Verify

```bash
gofmt -w .
go test -v -race ./...
go build -o bin/app ./cmd/app
```

The reply includes the test result and states whether PostgreSQL conformance ran.
