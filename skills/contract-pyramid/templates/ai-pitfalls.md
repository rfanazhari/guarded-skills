<!-- destination: `.context/00-AI-PITFALLS-AND-RULES.md` — do not copy this comment into the target -->

# AI operational guardrails

## 1. Do not agree past the contract

When a proposal adds a status, a field, or an error code, check it against `docs/` and the error table. If it is absent, ask. Record the trade-off of the user's proposal beside the rule already written.

## 2. Do not add an abstraction that has one caller

A port exists because two adapters or a test share it. A factory that wraps one constructor stays inline.

## 3. Do not invent symbols

Import only the language standard library and modules already declared by the project. If a library has no such API, say so.

## 4. Prove the change

A behavior change updates the scenario matrix first. Tests use an injected clock and id. The task is unfinished until the profile's test and build commands exit 0.
