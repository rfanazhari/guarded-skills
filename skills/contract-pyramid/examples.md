# Example

Input the user gave:

- Service Ledger, module `example.com/ledger`, port `8080`
- Aggregate Entry, a titled item
- Collection `entries`, table `ledger_entries`
- Create stores `DRAFT`
- Empty title is rejected
- Activate moves `DRAFT` to `ACTIVE`
- Activate when the entry is not `DRAFT` is rejected
- Delete removes the row
- Unknown id on read or delete is rejected

## Files written

```
AGENTS.md
docs/01-system-overview.md
docs/02-architecture-clean-layering.md
docs/03-api-spec.md
docs/04-database-design.md
docs/05-feature-addition-workflow.md
.context/00-AI-PITFALLS-AND-RULES.md
.context/01-PROJECT-CONTEXT.md
.context/02-SHARED-CONTRACTS/openapi-spec.yaml
.context/02-SHARED-CONTRACTS/error-codes.md
.context/backend/TECHNICAL-GUARDRAILS.md
.context/backend/business-matrix.md
.context/contracts/00-CONTRACTS-FRAMEWORK.md
.context/contracts/01-CODE-INTERFACE-CONTRACT.md
.context/contracts/02-API-NETWORK-CONTRACT.md
.context/contracts/03-PERSISTENCE-EVENT-CONTRACT.md
.context/contracts/04-BEHAVIORAL-STATE-CONTRACT.md
.context/contracts/05-WORKFLOW-OPERATIONAL-CONTRACT.md
```

## Scenario rows

| ID | Scenario | Input | Pre-condition | Outcome | HTTP |
| :--- | :--- | :--- | :--- | :--- | :--- |
| SC-01 | Create with title | `title` set | none | Entry in `DRAFT` | 201 |
| SC-02 | Create without title | blank title | none | `INVALID_INPUT` | 400 |
| SC-03 | Get existing | known id | entry exists | entry returned | 200 |
| SC-04 | Get missing | unknown id | none | `RESOURCE_NOT_FOUND` | 404 |
| SC-05 | Activate draft | id | status `DRAFT` | status `ACTIVE` | 200 |
| SC-06 | Activate when not draft | id | status is not `DRAFT` | `INVALID_STATE_TRANSITION` | 422 |
| SC-07 | Delete existing | id | entry exists | row removed | 200 |
| SC-08 | Delete missing | unknown id | none | `RESOURCE_NOT_FOUND` | 404 |

No archive, suspend, or reactivate row is written. The user did not ask for those transitions.

## Adjust mode

User: "Add a rule that activate requires a non-empty title."

Touch `04-BEHAVIORAL-STATE-CONTRACT.md` and the `SC-05` pre-condition only. Leave `SC-01` through `SC-04` and `SC-06` through `SC-08` as they are.
