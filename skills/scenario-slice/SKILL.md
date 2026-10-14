---
name: scenario-slice
description: >
  Adds one business scenario to a service that already has an AGENTS.md
  contract pack and an ArchForge skeleton. Updates the scenario matrix and
  OpenAPI document, then the domain, a failing use case test, both
  repository adapters, the HTTP handler, and the composition root. Use
  when the user says "scenario slice", "tambah skenario", "tambah baris
  SC", "tambah endpoint", "ubah transisi status", or "ikuti urutan tambah
  fitur". Refuses to run when AGENTS.md is missing. Do not use to create
  a new service.
---

# scenario-slice

Add one scenario to an existing service. One invocation is one `SC-NN` row, carried through every layer that enforces it.

## Before starting

Read `AGENTS.md`. If it is missing, stop and tell the user to run `contract-pyramid`, then `archforge-scaffold` when they also need a new codebase.

Read the stack named in `AGENTS.md`. For Go, Gin, and GORM, follow [profiles/go-gin-gorm.md](profiles/go-gin-gorm.md). If `AGENTS.md` names a stack with no profile file here, stop and ask.

When the scenario adds a status, a transition, a field, a route, or an error code, run `contract-pyramid` in adjust mode first. Do not start code while the OpenAPI document, the error table, and the behavioral contract still describe the old rule.

## Required input

| Input | Ask when missing |
| :--- | :--- |
| Scenario in one sentence | always |
| Input and pre-condition | always |
| Outcome and HTTP status | always |
| Error code, if it fails | when the outcome is an error |

The next free id is the next `SC-NN` after the highest id already in `.context/backend/business-matrix.md`. Do not reuse or renumber an id.

## Modes

**create.** A new row and the code that makes it true.

**adjust.** The user named an existing `SC-NN`. Change that row and the tests and branches that cite it. Leave other rows as they are.

**revise.** A requirement replaced a rule. Update the contract row, then the entity method, then the tests. Delete a route only when the OpenAPI document no longer lists it.

## Procedure

Follow [checklist.md](checklist.md). Use [conformance.md](conformance.md) when the scenario touches the repository port. A worked change is in [examples.md](examples.md).

## Done

The definition of done in `.context/contracts/05-WORKFLOW-OPERATIONAL-CONTRACT.md` is met. Paste the test and build output. If the durable-store conformance test was skipped, say it was not verified.
