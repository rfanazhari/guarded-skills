<!-- destination: `.context/backend/business-matrix.md` — do not copy this comment into the target -->

# Business scenario matrix — {{Service}}

Write one row per behavior the user stated. Replace the sample rows below so the matrix matches that input. The test name carries the same id.

| ID | Scenario | Input | Pre-condition | Expected outcome | HTTP |
| :--- | :--- | :--- | :--- | :--- | :--- |
| SC-01 | Create {{Aggregate}} | valid title | none | stored in the starting status | 201 |
| SC-02 | Create {{Aggregate}} | blank title | none | `INVALID_INPUT` | 400 |

Add further rows from the agreed lifecycle. Do not add a row for a transition that the behavioral contract does not list.
