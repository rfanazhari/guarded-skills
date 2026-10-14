<!-- destination: `docs/05-feature-addition-workflow.md` — do not copy this comment into the target -->

# Feature addition

One scenario is one pass through this order:

```
docs and .context  →  domain  →  failing use case test  →  use case
  →  both adapters  →  delivery  →  composition root  →  verify
```

## 1. Contract

Update `docs/` when the behavior changed. Update the OpenAPI document when the HTTP shape changed. Add or edit the `SC-NN` row in `.context/backend/business-matrix.md`. Add an error code only in the error table.

## 2. Domain

Entity methods enforce the invariants and return sentinel errors. The repository port is the only persistence API the use case sees. The port takes a context first.

## 3. Failing test

Write the table test first, in the use case package's external test package. Inject a fixed clock and a fixed id. Name the case with the scenario id. Run the test and see it fail for the missing behavior.

## 4. Use case

Implement the method until that test passes. Call the injected clock and id. Do not read the wall clock on a path the test asserts.

## 5. Adapters

Implement the port twice. Not-found, conflict, and an empty page behave the same in both.

## 6. Delivery

Decode the request into a camelCase DTO. Pass the request context to the use case. Map errors through the shared envelope helper.

## 7. Composition

Construct the adapter the configuration selects, then the use case, then the handler. Register the route.

## 8. Verify

Run the profile's format, test, and build commands. Both exit 0. Report a skipped durable-store conformance run as not verified.
