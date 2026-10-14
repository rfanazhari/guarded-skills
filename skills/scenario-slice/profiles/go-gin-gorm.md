# Profile: go-gin-gorm

Use this file when `AGENTS.md` names Go, Gin, and GORM. Paths match the tree `archforge-scaffold` wrote. Replace `{{aggregate}}`, `{{Aggregate}}`, and `{{module}}` from that project.

## Where a scenario lands

| Change | File |
| :--- | :--- |
| Status value | `domain/enum/status.go` |
| Invariant | `domain/entity/{{aggregate}}.go` |
| New port method | `domain/repository/{{aggregate}}_repository.go` and both adapters |
| Use case | `application/usecase/{{aggregate}}/{{aggregate}}_usecase.go` |
| Scenario test | `application/usecase/{{aggregate}}/{{aggregate}}_usecase_test.go`, package `{{aggregate}}_test` |
| Column | GORM model in `infrastructure/repository/{{aggregate}}_gorm_repository.go` and the memory clone fields on the entity |
| HTTP | `delivery/http/{{aggregate}}handler/dto.go`, `handler.go`, `handler_test.go`, and `delivery/http/router.go` |
| New sentinel | `pkg/errs/errs.go` and the switch in `pkg/httpresponse/response.go`, after the error table has the row |

## Test shape

```go
fixedTime := time.Date(2026, 10, 14, 12, 0, 0, 0, time.UTC)
fixedID := "test-{{aggregate}}-id-1"

uc := {{aggregate}}.NewUsecaseWithClock(repo,
    func() string { return fixedID },
    func() time.Time { return fixedTime },
)
```

Assert errors with `assert.ErrorIs` and a sentinel from `{{module}}/pkg/errs`.

The mock in the use case test is not `New{{Aggregate}}MemoryRepository`.

## Adapters

A new port method is implemented in `{{aggregate}}_memory_repository.go` and `{{aggregate}}_gorm_repository.go` in the same change.

Memory methods lock `sync.RWMutex` and store clones.

GORM methods use `WithContext`. `ErrRecordNotFound` becomes `errs.ErrNotFound`. `ErrDuplicatedKey` becomes `errs.ErrConflict`. The database is opened with `TranslateError: true`.

Startup `AutoMigrate` stays in the GORM constructor. Do not add a migration directory.

## Handler test

Stub the use case interface. Assert the HTTP status and `error.code` for the failure this scenario names. Assert `success` and the response field for the success this scenario names.

## Commands

Use the commands in `AGENTS.md`. When they are missing, use:

```bash
gofmt -w .
go test -v -race ./...
go build -o bin/app ./cmd/app
```

Set `CONTRACT_DATABASE_DSN` to run the PostgreSQL half of `{{aggregate}}_conformance_test.go`. When it is unset, report that half as not verified.
