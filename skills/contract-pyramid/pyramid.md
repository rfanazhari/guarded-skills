# Five-layer contract pyramid

A contract records a pre-condition, a post-condition, and the invariant that must stay true. Write the layer at the altitude of the rule. Do not copy a rule into a second layer with different wording.

| Layer | File | Governs | Enforcer |
| :--- | :--- | :--- | :--- |
| 1. Code and interface | `01-CODE-INTERFACE-CONTRACT.md` | Signatures, sentinel errors, domain purity | Compiler |
| 2. API and network | `02-API-NETWORK-CONTRACT.md` | Methods, envelopes, status codes, JSON names | OpenAPI document plus handler tests |
| 3. Persistence | `03-PERSISTENCE-EVENT-CONTRACT.md` | Identity, uniqueness, timestamps, delete behavior | The two repository adapters |
| 4. Behavior and state | `04-BEHAVIORAL-STATE-CONTRACT.md` | Legal transitions and scenario rows | Table-driven use case tests |
| 5. Workflow | `05-WORKFLOW-OPERATIONAL-CONTRACT.md` | Order of work and definition of done | The agent running `scenario-slice` |

## Rules for writing a layer

- Layer 1 names the repository port and the sentinel errors. It does not name tables or HTTP.
- Layer 2 owns the error-code strings and the HTTP status for each string. Application code maps sentinels to those strings. It does not invent a third list.
- Layer 3 describes storage behavior. A SQL sketch may appear as an illustration. The binding rule is the behavior: hard delete or soft delete, unique identity, timestamps.
- Layer 4 lists only transitions the service will implement. A picture that shows a transition with no pre-condition is incomplete.
- Layer 5 tells the agent to update layer 2 and layer 4 before changing code, and to finish only when tests and the build exit 0.

## Single error table

`.context/02-SHARED-CONTRACTS/error-codes.md` is the only list of error codes. The OpenAPI error schema, the API contract, and later the HTTP mapper all quote that table. Adding a code means adding one row there first.
