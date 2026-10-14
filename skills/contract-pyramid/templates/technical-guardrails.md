<!-- destination: `.context/backend/TECHNICAL-GUARDRAILS.md` — do not copy this comment into the target -->

# Technical guardrails — {{Service}}

## Architecture

```
cmd/app
domain/entity
domain/enum
domain/repository
application/usecase/{{aggregate}}
delivery
infrastructure
pkg/config
pkg/errs
pkg/httpresponse
```

Create a value-object package only when the behavioral contract names a value object. Do not create an empty package.

## Invariants

1. Outer layers depend on inner layers. `domain/` does not import transport or database packages.
2. Transport DTO fields use camelCase JSON names.
3. Sentinel errors are declared once and mapped through the error table.
4. Business functions return a result and an error. They do not panic.
5. Tests that assert time or identity use injected functions.

## Stack profile

This section is filled by `archforge-scaffold` when a profile is applied. Until then, name no framework.
