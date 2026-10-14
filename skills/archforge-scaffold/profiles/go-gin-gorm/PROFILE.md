# Profile: go-gin-gorm

Stack lock for this profile: Go 1.22 or newer, Gin, GORM, PostgreSQL as the durable driver, and an in-memory adapter as the default.

## Go version

Run `go version`. Use that version when it is 1.22 or newer. When it is older, stop and ask. Write the chosen version into `go.mod` and into the `FROM golang:{{go_version}}-alpine` build image. Do not silently downgrade language features to an older Go than the one you recorded.

## Modules

Declare these modules and let `go mod tidy` record versions:

- `github.com/gin-gonic/gin`
- `github.com/google/uuid`
- `github.com/joho/godotenv`
- `github.com/stretchr/testify`
- `gorm.io/gorm`
- `gorm.io/driver/postgres`

Add no other module.

## Layout

```
cmd/app/main.go
domain/entity/{{aggregate}}.go
domain/enum/status.go
domain/repository/{{aggregate}}_repository.go
application/usecase/{{aggregate}}/{{aggregate}}_usecase.go
application/usecase/{{aggregate}}/{{aggregate}}_usecase_test.go
infrastructure/database/postgres.go
infrastructure/repository/{{aggregate}}_gorm_repository.go
infrastructure/repository/{{aggregate}}_memory_repository.go
infrastructure/repository/{{aggregate}}_conformance_test.go
delivery/http/router.go
delivery/http/middleware/middleware.go
delivery/http/healthhandler/handler.go
delivery/http/{{aggregate}}handler/dto.go
delivery/http/{{aggregate}}handler/handler.go
delivery/http/{{aggregate}}handler/handler_test.go
pkg/config/config.go
pkg/errs/errs.go
pkg/httpresponse/response.go
```

File bodies are in [domain.md](domain.md), [application.md](application.md), [persistence.md](persistence.md), [delivery.md](delivery.md), and [composition.md](composition.md).

## Commands to write into AGENTS.md

```bash
go test -v -race ./...
go build -o bin/app ./cmd/app
go run ./cmd/app
```

`DB_DRIVER=memory` is the default, so the run command needs no database.

## Verify before finishing

```bash
gofmt -w .
go test -v -race ./...
go build -o bin/app ./cmd/app
```

All three exit 0. In the reply, state whether `CONTRACT_DATABASE_DSN` was set. When it was unset, PostgreSQL conformance was not verified.

## Stack section for the guardrails file

Append this under "Stack profile" in `.context/backend/TECHNICAL-GUARDRAILS.md`:

- Language: Go {{go_version}}
- HTTP: `github.com/gin-gonic/gin`
- Durable store: `gorm.io/gorm` with `gorm.io/driver/postgres`
- Default store: in-memory adapter
- Id: `github.com/google/uuid`
- Config: environment variables, with `github.com/joho/godotenv` reading a local `.env` when one is present. The repository ships `.env.example` only.
