<!-- destination: `.context/contracts/01-CODE-INTERFACE-CONTRACT.md` — do not copy this comment into the target -->

# Code and interface

## Rules

- The domain package tree is `domain/`. It imports the standard library, other `domain` packages, and `{{module}}/pkg/errs`. It imports no transport framework and no database driver.
- Repository methods and use case methods take a context as the first parameter.
- On failure, return a nil result and a sentinel from `pkg/errs`.
- Do not format a new error string at the call site for a case the error table already names.

## Sentinels

| Sentinel | Error table code |
| :--- | :--- |
| `ErrInvalidInput` | `INVALID_INPUT` |
| `ErrUnauthorized` | `UNAUTHORIZED` |
| `ErrForbidden` | `FORBIDDEN` |
| `ErrNotFound` | `RESOURCE_NOT_FOUND` |
| `ErrConflict` | `RESOURCE_ALREADY_EXISTS` |
| `ErrInvalidStateTransition` | `INVALID_STATE_TRANSITION` |
| `ErrInternal` | `INTERNAL_SERVER_ERROR` |

## Port

`{{Aggregate}}Repository` is declared in `domain/repository` and implemented twice in `infrastructure/repository`. The use case depends on the interface.
