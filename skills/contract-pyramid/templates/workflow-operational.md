<!-- destination: `.context/contracts/05-WORKFLOW-OPERATIONAL-CONTRACT.md` — do not copy this comment into the target -->

# Workflow

## Order

`docs/` and `.context/` first, then domain, then a failing use case test, then the use case, then both adapters, then delivery, then the composition root.

## Definition of done

- Contract documents match the behavior in the same change.
- Each touched `SC-NN` row has a use case test with a fixed clock and a fixed id.
- A handler test covers the success envelope and one error mapping for each new route.
- Both adapters pass the same not-found, conflict, and empty-page checks, or the durable adapter is reported as not verified.
- The profile format, test, and build commands exit 0.
- No real environment file is committed.

## Failure handling

Return `(result, error)`. Do not panic for a bad request, a missing row, or an illegal transition.
