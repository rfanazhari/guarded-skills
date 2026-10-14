---
name: salt-scaffold
description: >
  Scaffolds a runnable Go service on the SALT layering (cmd, domain,
  application, delivery, infrastructure, pkg) with one sample aggregate,
  an in-memory adapter, a PostgreSQL GORM adapter, a JSON envelope, and
  injected clock and id. Use when the user says "salt scaffold",
  "scaffold service SALT", "buat boilerplate backend", or "buat service
  dengan adapter memori dan Postgres". Requires a contract-pyramid pack
  in the target project. Do not use to add a later feature, and do not
  use for a stack that has no profile in this skill.
---

# salt-scaffold

Turn a finished `contract-pyramid` pack into a Go service that tests and builds. The sample aggregate is the one named in that pack. This skill runs once per service.

## Required input

Ask for anything missing.

| Input | Default |
| :--- | :--- |
| Target project root that already contains `AGENTS.md` | none |
| Go version | the installed `go version`, when it is 1.22 or newer |
| Profile | `go-gin-gorm` |

Stop if `AGENTS.md` or `.context/backend/business-matrix.md` is missing. Tell the user to run `contract-pyramid` first. Do not invent the pack inside this skill.

Stop if the requested stack has no folder under [profiles/](profiles/). Ask for a profile. Do not invent Nest, Spring, or another tree.

## Modes

**create.** Write the Go tree for the aggregate named in the behavioral contract. Stop if `cmd/app/main.go` already exists.

**adjust.** Change only the foundation the user named: Go version, port, module path, or a profile file. Do not regenerate use case methods the user did not mention.

A new scenario is `scenario-slice`, not an adjust of this skill.

## Procedure

1. Read `AGENTS.md`, `docs/02-architecture-clean-layering.md`, `docs/04-database-design.md`, `.context/contracts/04-BEHAVIORAL-STATE-CONTRACT.md`, and `.context/02-SHARED-CONTRACTS/error-codes.md`.
2. Read [exclusions.md](exclusions.md) and the profile files:
   - [profiles/go-gin-gorm/PROFILE.md](profiles/go-gin-gorm/PROFILE.md)
   - [profiles/go-gin-gorm/domain.md](profiles/go-gin-gorm/domain.md)
   - [profiles/go-gin-gorm/application.md](profiles/go-gin-gorm/application.md)
   - [profiles/go-gin-gorm/persistence.md](profiles/go-gin-gorm/persistence.md)
   - [profiles/go-gin-gorm/delivery.md](profiles/go-gin-gorm/delivery.md)
   - [profiles/go-gin-gorm/composition.md](profiles/go-gin-gorm/composition.md)
3. Implement only the transitions written in the behavioral contract. The default sample is create in `DRAFT`, activate from `DRAFT` to `ACTIVE`, get, list, and hard delete.
4. Fill the "Stack profile" section of `.context/backend/TECHNICAL-GUARDRAILS.md` and the commands section of `AGENTS.md` from `PROFILE.md`.
5. Tick every item in [exclusions.md](exclusions.md).
6. Run the verify commands in `PROFILE.md`. Paste the results into the reply.

## Report

Say whether the durable-store conformance test ran or was skipped. A skip is not a pass. Memory conformance must pass.
