# Example

`contract-pyramid` already wrote the Ledger pack: aggregate Entry, module `example.com/ledger`, collection `entries`, table `ledger_entries`, port `8080`, hard delete, activate only from `DRAFT`.

The user says: "salt scaffold". Go on the machine is 1.22 or newer. Profile `go-gin-gorm`.

## Tree added

```
cmd/app/main.go
domain/entity/entry.go
domain/enum/status.go
domain/repository/entry_repository.go
application/usecase/entry/entry_usecase.go
application/usecase/entry/entry_usecase_test.go
infrastructure/database/postgres.go
infrastructure/repository/entry_gorm_repository.go
infrastructure/repository/entry_memory_repository.go
infrastructure/repository/entry_conformance_test.go
delivery/http/router.go
delivery/http/middleware/middleware.go
delivery/http/healthhandler/handler.go
delivery/http/entryhandler/dto.go
delivery/http/entryhandler/handler.go
delivery/http/entryhandler/handler_test.go
pkg/config/config.go
pkg/errs/errs.go
pkg/httpresponse/response.go
go.mod
Makefile
Dockerfile
docker-compose.yml
.env.example
```

`AGENTS.md` gains the Go test and build commands. The guardrails file names Gin, GORM, and PostgreSQL in its stack section.

## Verify

From the module root:

```bash
gofmt -w .
go test -v -race ./...
go build -o bin/app ./cmd/app
```

Memory conformance runs inside `go test`. PostgreSQL conformance runs only when `CONTRACT_DATABASE_DSN` is set. If it is unset, the reply says the durable adapter was not verified.

## What is not in the tree

No archive route, no `deleted_at`, no `metadata` column, no `.env`, no empty `domain/valueobject` package.
