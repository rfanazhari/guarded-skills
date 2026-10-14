<!-- destination: `.context/contracts/04-BEHAVIORAL-STATE-CONTRACT.md` — do not copy this comment into the target -->

# Behavior and state — {{Aggregate}}

Document only the transitions the user requested. The sample below is the default lifecycle. Delete a transition the user did not ask for. Add a transition only in revise or adjust mode when the user names it.

## States

- `DRAFT` is the status of a newly created `{{Aggregate}}`.
- `ACTIVE` is the status after a successful activate.

## Transitions

| From | To | Action | Pre-condition | On failure |
| :--- | :--- | :--- | :--- | :--- |
| none | `DRAFT` | Create | title non-empty after trim | `ErrInvalidInput` |
| `DRAFT` | `ACTIVE` | Activate | current status is `DRAFT` | `ErrInvalidStateTransition` |

Any activate that starts from a status other than `DRAFT` returns `ErrInvalidStateTransition`.

## Scenarios

The rows in `.context/backend/business-matrix.md` are the cases the use case tests name. A row without a test is unfinished. A test without a row is an invented rule.
