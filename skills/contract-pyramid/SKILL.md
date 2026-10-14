---
name: contract-pyramid
description: >
  Writes a stack-agnostic context-engineering pack for a backend service:
  AGENTS.md, a docs source of truth, a five-layer contract pyramid, a
  business scenario matrix, and one error-code table. Use when the user
  says "contract pyramid", "susun kontrak lima lapis", "pasang context
  engineering", "buat AGENTS.md dan business matrix", "ubah state machine
  di kontrak", or "tambah kode error ke kontrak". Use before scaffolding
  code. Do not use to add a feature to an existing service or to generate
  application source files.
---

# contract-pyramid

Write the rules for a service before any feature code exists. The pack is the source of truth that `archforge-scaffold` and `scenario-slice` read.

The pack is stack-agnostic. Name no web framework, ORM, or driver in the contracts. Record the persistence story as behavior: identity, uniqueness, timestamps, and whether delete removes the row.

## Required input

Ask for anything missing. Do not guess a state machine or an error code.

| Input | Example |
| :--- | :--- |
| Service name | Ledger |
| Module path | `example.com/ledger` |
| Aggregate name and meaning | Entry, a titled item with a lifecycle |
| Collection path segment | `entries` |
| Starting state | `DRAFT` |
| One legal transition and one illegal transition | `DRAFT` to `ACTIVE`; activate again is illegal |
| Delete behavior | hard delete |
| Listen port | `8080` |

If the user has no lifecycle, write the aggregate as stateless and leave the state diagram empty. Do not invent `SUSPENDED`, `ARCHIVED`, or a reactivate action.

## Modes

**create.** The target root has no `AGENTS.md`. Write every file in the output map.

**adjust.** `AGENTS.md` is already there. Edit only the sections the user named. Do not regenerate the business matrix. Do not renumber or delete a `SC-` id.

**revise.** A requirement changed. Update the scenario rows, the error table, and the state contract that the change touches. Then list the files and the rule that moved.

## Procedure

1. Confirm the required input.
2. Read [pyramid.md](pyramid.md) and [examples.md](examples.md).
3. Copy each file under [templates/](templates/) to the path named in its leading destination comment. Do not copy that comment. Replace every token listed in the pack README.
4. Write scenario rows only for behavior the user stated. Number them `SC-01` upward. Each row has id, scenario, input, pre-condition, outcome, and HTTP status.
5. Add an error code only by appending a row to the error table. The same code string appears in the API contract and the OpenAPI error schema.
6. In the state contract, document a transition only when the user asked for it. Record the pre-condition and the error returned when the pre-condition fails.
7. State delete as hard delete unless the user asked for soft delete. If they asked for soft delete, the contract says which timestamp is set and that reads hide the row.

## Output map

| Template | Destination |
| :--- | :--- |
| [templates/agents.md](templates/agents.md) | `AGENTS.md` |
| [templates/docs-overview.md](templates/docs-overview.md) | `docs/01-system-overview.md` |
| [templates/docs-architecture.md](templates/docs-architecture.md) | `docs/02-architecture-clean-layering.md` |
| [templates/docs-api.md](templates/docs-api.md) | `docs/03-api-spec.md` |
| [templates/docs-persistence.md](templates/docs-persistence.md) | `docs/04-database-design.md` |
| [templates/docs-feature-workflow.md](templates/docs-feature-workflow.md) | `docs/05-feature-addition-workflow.md` |
| [templates/ai-pitfalls.md](templates/ai-pitfalls.md) | `.context/00-AI-PITFALLS-AND-RULES.md` |
| [templates/project-context.md](templates/project-context.md) | `.context/01-PROJECT-CONTEXT.md` |
| [templates/openapi.yaml](templates/openapi.yaml) | `.context/02-SHARED-CONTRACTS/openapi-spec.yaml` |
| [templates/error-codes.md](templates/error-codes.md) | `.context/02-SHARED-CONTRACTS/error-codes.md` |
| [templates/technical-guardrails.md](templates/technical-guardrails.md) | `.context/backend/TECHNICAL-GUARDRAILS.md` |
| [templates/business-matrix.md](templates/business-matrix.md) | `.context/backend/business-matrix.md` |
| [templates/contracts-framework.md](templates/contracts-framework.md) | `.context/contracts/00-CONTRACTS-FRAMEWORK.md` |
| [templates/code-interface.md](templates/code-interface.md) | `.context/contracts/01-CODE-INTERFACE-CONTRACT.md` |
| [templates/api-network.md](templates/api-network.md) | `.context/contracts/02-API-NETWORK-CONTRACT.md` |
| [templates/persistence-event.md](templates/persistence-event.md) | `.context/contracts/03-PERSISTENCE-EVENT-CONTRACT.md` |
| [templates/behavioral-state.md](templates/behavioral-state.md) | `.context/contracts/04-BEHAVIORAL-STATE-CONTRACT.md` |
| [templates/workflow-operational.md](templates/workflow-operational.md) | `.context/contracts/05-WORKFLOW-OPERATIONAL-CONTRACT.md` |

## Stop rules

- Stop when a transition, field, or error code was not given.
- Stop when an existing `docs/` file, `.context/` file, and the code disagree. Report the conflict. Do not pick a side in this skill.
- In adjust mode, stop rather than rewrite a scenario the user did not mention.

## Handoff

When the pack is in place, application code is a separate skill: `archforge-scaffold` for a new service, `scenario-slice` for a later scenario. This skill does not write Go, Node, or Java files.
