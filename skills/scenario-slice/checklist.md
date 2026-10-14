# Scenario checklist

Copy this into the working notes and tick it as you go.

```
- [ ] 1. Contract row and HTTP shape
- [ ] 2. Domain rule
- [ ] 3. Failing use case test
- [ ] 4. Use case method
- [ ] 5. Both adapters, when the port changed
- [ ] 6. Delivery DTO, handler, route, handler test
- [ ] 7. Composition wiring, when a new type was constructed
- [ ] 8. Format, test, build
```

## 1. Contract

Edit `.context/backend/business-matrix.md` for this `SC-NN` only.

Edit `.context/02-SHARED-CONTRACTS/openapi-spec.yaml` when the route or body changed.

Edit `.context/contracts/04-BEHAVIORAL-STATE-CONTRACT.md` when a transition changed.

Append to `.context/02-SHARED-CONTRACTS/error-codes.md` only for a new code. Then add the sentinel and the HTTP branch that quote that row.

Update the `docs/` page that owns the rule so it matches the contract.

## 2. Domain

Put the invariant on the entity. Return a sentinel from `pkg/errs`. Add a port method only when the use case cannot express the scenario with the methods already on the port.

## 3. Failing test

External test package. Fixed clock and fixed id. The `t.Run` name starts with `SC-NN:`.

Run the package tests and confirm the new case fails because the behavior is absent.

## 4. Use case

Implement until that case passes. Use the injected clock and id on the asserted path.

## 5. Adapters

When the port gained or changed a method, update both adapters in the same change. Run the conformance cases in [conformance.md](conformance.md).

## 6. Delivery

camelCase JSON. Request context passed into the use case. Errors mapped by the shared helper. Add a handler test for the success status and for the error status of this scenario.

## 7. Composition

Register a new handler or route in the existing composition root. Do not put the rule in `main`.

## 8. Verify

Run the format, test, and build commands recorded in `AGENTS.md`. Both the test and the build exit 0.
