<!-- destination: `.context/contracts/00-CONTRACTS-FRAMEWORK.md` — do not copy this comment into the target -->

# Contract pyramid — {{Service}}

| Layer | Document | Scope | Enforcer |
| :--- | :--- | :--- | :--- |
| 1 | `01-CODE-INTERFACE-CONTRACT.md` | Signatures and sentinel errors | Compiler |
| 2 | `02-API-NETWORK-CONTRACT.md` | HTTP and JSON | OpenAPI document and handler tests |
| 3 | `03-PERSISTENCE-EVENT-CONTRACT.md` | Stored identity, uniqueness, delete | Both repository adapters |
| 4 | `04-BEHAVIORAL-STATE-CONTRACT.md` | Transitions and scenarios | Use case tests named `SC-NN` |
| 5 | `05-WORKFLOW-OPERATIONAL-CONTRACT.md` | Order of work and definition of done | Review before the change is called done |

A rule lives in one layer. The other layers cite it.
