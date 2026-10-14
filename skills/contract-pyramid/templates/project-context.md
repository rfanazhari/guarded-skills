<!-- destination: `.context/01-PROJECT-CONTEXT.md` — do not copy this comment into the target -->

# {{Service}} project context

Source of truth: `docs/`.

## Purpose

{{Service}} manages the `{{Aggregate}}` aggregate. The HTTP boundary, the state machine, and the delete behavior are defined in `docs/` and `.context/contracts/`.

## Rules

- Inner layers do not import outer layers.
- Domain rules do not import a transport or database framework.
- Two persistence adapters implement one port.
- Transport field names are camelCase.
- Error codes come from `.context/02-SHARED-CONTRACTS/error-codes.md`.

## Glossary

- **{{Aggregate}}.** The aggregate this service manages.
- **Status.** The lifecycle values listed in `.context/contracts/04-BEHAVIORAL-STATE-CONTRACT.md`.
- **Scenario id.** A row in `.context/backend/business-matrix.md`, formatted `SC-NN`.
- **Response envelope.** The success and error JSON shapes in `docs/03-api-spec.md`.

## When the rules change

1. Update the document in `docs/` that owns the rule.
2. Update this file's glossary if a term changed.
3. Update the OpenAPI document and the error table if the HTTP shape changed.
