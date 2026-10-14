# Composition files

`cmd/app/main.go` only loads config, picks an adapter, constructs the use case and handlers, and drains the server. No business rules.

## Config

`pkg/config/config.go` reads the environment. `godotenv.Load` may fail when no `.env` is present. Defaults:

| Variable | Default |
| :--- | :--- |
| `APP_ENV` | `development` |
| `PORT` | `{{port}}` |
| `GIN_MODE` | `debug` |
| `DB_DRIVER` | `memory` |
| `DB_HOST` | `localhost` |
| `DB_PORT` | `5432` |
| `DB_USER` | `postgres` |
| `DB_PASSWORD` | `postgres` |
| `DB_NAME` | `{{service}}` |
| `DB_SSLMODE` | `disable` |
| `CORS_ORIGIN` | `http://localhost:3000` |

`.env.example` lists those keys and the same defaults. Do not create `.env`.

## Main

When `DB_DRIVER` is `postgres`, open PostgreSQL and construct the GORM repository. Any other value constructs the memory repository and logs that the process is running without a database.

Then `NewUsecase`, the health handler, the aggregate handler, and `SetupRouter`.

Listen on `:` plus the port. On `SIGINT` or `SIGTERM`, shut down with a 5 second timeout.

## Makefile

```makefile
.PHONY: all build run test tidy lint clean

all: test build
tidy:
	go mod tidy
build:
	go build -o bin/app ./cmd/app
run:
	go run ./cmd/app
test:
	go test -v -race ./...
lint:
	go vet ./...
clean:
	rm -rf bin/
```

## Container

The build stage uses `golang:{{go_version}}-alpine` and `CGO_ENABLED=0`. The runtime stage copies the binary only. It does not copy `.env` or `.env.example`.

Compose may start the app with `DB_DRIVER=postgres` and a Postgres service. Those values live in the compose file, not in a committed `.env`.

## go.mod

```
module {{module}}

go {{go_version}}
```

Run `go get` for the modules listed in [PROFILE.md](PROFILE.md), then `go mod tidy`.
