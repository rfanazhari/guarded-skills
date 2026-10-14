# Adapter conformance

Run these cases whenever a scenario adds or changes a repository method. Run them against every adapter the port has.

The use case unit test keeps its hand-written mock. These cases use the real adapters.

## Cases that always apply

1. **Missing id.** `FindByID` and `Delete` of an id that was never stored return the not-found sentinel.
2. **Duplicate id.** `Create` of an id that is already stored returns the conflict sentinel.
3. **Page past the end.** With one stored row, `FindAll` at an offset beyond that row returns a non-nil empty slice and a total of one.

## Cases this scenario adds

Add a fourth case only for behavior this scenario introduced. Example: a soft-delete scenario asserts that a deleted id is not returned by `FindByID` on both adapters. Do not add that case while delete is still a hard delete.

## How to run

The Go profile keeps these cases in `infrastructure/repository/{{aggregate}}_conformance_test.go`.

- The memory adapter always runs.
- The durable adapter runs when `CONTRACT_DATABASE_DSN` is set.
- When that variable is empty, the test skips the durable adapter. The reply to the user says the durable adapter was not verified. A skip is not reported as a pass.
