<!-- destination: `docs/02-architecture-clean-layering.md` — do not copy this comment into the target -->

# Architecture

Dependencies point inward.

| Layer | Path | Responsibility | Rule |
| :--- | :--- | :--- | :--- |
| Composition | `cmd/app` | Load config, choose an adapter, start and drain the server | No business rules |
| Domain | `domain` | Entities, enums, repository ports | No transport or database imports |
| Application | `application/usecase` | One use case per action, with table tests | Calls ports. Uses an injected clock and id |
| Delivery | `delivery` | Decode the request, call the use case, write the envelope | Does not touch storage |
| Infrastructure | `infrastructure` | Adapters that implement the ports | Maps storage failures to sentinel errors |
| Shared | `pkg` | Config, sentinel errors, envelope writer | No aggregate rules |

`application` may import `domain` and `pkg`. `delivery` and `infrastructure` may import `application` and `domain`. `domain` does not import `delivery` or `infrastructure`.
