# GUARDED-SKILLS

> Don't just generate a service. Guard it.

**Engineering skills for building services rules-first: write the contract, scaffold a runnable service from it, then extend it one scenario at a time.**

GUARDED-SKILLS is a collection of reusable skills for AI coding agents that helps engineers move from **explicit rules to a running service, and from a running service to new behavior, without drifting away from those rules**.

Instead of generating a service and hoping it matches the requirements, GUARDED-SKILLS enforces a fixed order:

```text
Rules (Contract)
      ↓
Runnable Service
      ↓
One Scenario at a Time
```

The objective is simple:

> **Write the rules first. Generate from the rules. Extend one scenario at a time.**

---

## Installation

Install GUARDED-SKILLS using the [`skills`](https://skills.sh) CLI.

### Install all skills

```bash
npx skills add rfanazhari/guarded-skills
```

### Install a specific skill

```bash
npx skills add rfanazhari/guarded-skills --skill contract-pyramid
```

```bash
npx skills add rfanazhari/guarded-skills --skill archforge-scaffold
```

```bash
npx skills add rfanazhari/guarded-skills --skill scenario-slice
```

### Update installed skills

```bash
npx skills update
```

Restart your AI coding agent after installation or update so the latest skills are loaded.

### Manual installation

Copy a skill folder into the agent skills directory.

```bash
cp -r skills/contract-pyramid skills/archforge-scaffold skills/scenario-slice <agent-skills-dir>/
```

---

# Skills

GUARDED-SKILLS currently provides three skills, intended to be used in this order.

## 1. Contract Pyramid

**Skill:** `contract-pyramid`

Writes the rules of the service before any feature code exists. The pack is the source of truth that `archforge-scaffold` and `scenario-slice` read.

The skill is **stack-agnostic**. It names no web framework, ORM, or driver. Persistence is recorded as behavior: identity, uniqueness, timestamps, and whether delete removes the row.

The pack includes:

```text
AGENTS.md
docs/          system overview, layering, API, database, feature workflow
.context/      pitfalls, project context, OpenAPI, error codes, guardrails
               business scenario matrix (SC-NN)
               five-layer contract pyramid
```

The five layers, each written once:

| Layer | Governs |
| :--- | :--- |
| Code and interface | Signatures, sentinel errors, domain purity |
| API and network | Methods, envelopes, status codes, JSON names |
| Persistence | Identity, uniqueness, timestamps, delete behavior |
| Behavior and state | Legal transitions and scenario rows |
| Workflow | Order of work and definition of done |

### Use for

* Defining the rules of a new service
* Making business and technical rules explicit before code exists
* Writing `AGENTS.md` and the business scenario matrix
* Changing a state machine or adding an error code in the contract
* Updating requirements by changing the contract first

### Trigger phrases

```text
contract pyramid
susun kontrak lima lapis
pasang context engineering
buat AGENTS.md dan business matrix
ubah state machine di kontrak
tambah kode error ke kontrak
```

Example:

```text
contract pyramid

Service: Ledger
Module: example.com/ledger
Aggregate: Entry, a titled item with a lifecycle
Collection: entries
Starting state: DRAFT
Legal: DRAFT to ACTIVE
Illegal: activate again
Delete: hard delete
Port: 8080
```

This skill does not add a feature to an existing service and does not generate application source files. If there is no lifecycle, the aggregate is written as stateless. States and error codes that were not given are not invented.

---

## 2. ArchForge Scaffold

**Skill:** `archforge-scaffold`

Turns a finished `contract-pyramid` pack into a runnable Go service. It runs once per service.

The Go tree follows ArchForge layering:

```text
cmd
domain
application
delivery
infrastructure
pkg
```

The `go-gin-gorm` profile writes one sample aggregate, an in-memory adapter, a PostgreSQL GORM adapter, a JSON envelope, and injected clock and id. Only transitions written in the behavioral contract are implemented. The default sample is create in `DRAFT`, activate from `DRAFT` to `ACTIVE`, get, list, and hard delete.

The skill stops when `AGENTS.md` or `.context/backend/business-matrix.md` is missing, and tells the user to run `contract-pyramid` first. It also stops when the requested stack has no folder under `profiles/`.

### Use for

* Starting a new service from an existing contract
* Producing a service skeleton that already follows the rules
* Scaffolding a backend with both a memory adapter and a Postgres adapter

### Trigger phrases

```text
ArchForge scaffold
scaffold service ArchForge
buat boilerplate backend
buat service dengan adapter memori dan Postgres
```

Example:

```text
ArchForge scaffold

Target: ./ledger
Profile: go-gin-gorm
```

A later feature is `scenario-slice`, not an adjust of this skill.

---

## 3. Scenario Slice

**Skill:** `scenario-slice`

Adds one business scenario to a service that already has an `AGENTS.md` contract pack and an ArchForge skeleton. One invocation is one `SC-NN` row, carried through every layer that enforces it.

The slice updates the scenario matrix and the OpenAPI document, then the domain, a failing use case test, both repository adapters, the HTTP handler, and the composition root.

When the scenario adds a status, a transition, a field, a route, or an error code, `contract-pyramid` runs in adjust mode first. Code does not start while the OpenAPI document, the error table, and the behavioral contract still describe the old rule.

The skill refuses to run when `AGENTS.md` is missing. If `AGENTS.md` names a stack with no profile, it stops and asks.

### Use for

* Extending an existing service by a single scenario
* Adding an `SC` row or an endpoint
* Changing a status transition
* Keeping every change small, focused, and traceable to a rule

### Trigger phrases

```text
scenario slice
tambah skenario
tambah baris SC
tambah endpoint
ubah transisi status
ikuti urutan tambah fitur
```

Example:

```text
scenario slice

Suspend an ACTIVE entry.
Input: entry id
Pre-condition: status is ACTIVE
Outcome: status becomes SUSPENDED
HTTP: 200
Error when not ACTIVE: ENTRY_NOT_ACTIVE
```

This skill does not create a new service.

---

# Modes

Each skill supports the same three modes.

| Mode | Behavior |
| :--- | :--- |
| `create` | Writes files that do not exist yet. Stops if the destination file already exists. |
| `adjust` | Edits only the sections the user named. Every other scenario row and file stays untouched. |
| `revise` | Applies a requirement change. Updates the contract first, then the code that enforces it. Lists every file touched and the rule that changed. |

For `contract-pyramid`, `create` requires a target root with no `AGENTS.md`. For `archforge-scaffold`, `create` stops if `cmd/app/main.go` already exists. For `scenario-slice`, `create` is a new `SC-NN` row and the code that makes it true; `adjust` changes only the named row.

---

# Engineering Workflows

The skills are designed to work in sequence.

## New Service

```text
Requirements
      ↓
Contract Pyramid
      ↓
Rules (AGENTS.md + five-layer pack)
      ↓
ArchForge Scaffold
      ↓
Runnable Go Service
      ↓
Tests + build
```

ArchForge Scaffold reports whether the durable-store conformance test ran or was skipped. A skip is not a pass. Memory conformance must pass.

---

## New Scenario on an Existing Service

```text
Existing Rules + Service
      ↓
Contract Pyramid (adjust, when the rule itself changes)
      ↓
Scenario Slice
      ↓
One New Scenario
      ↓
Tests + build
```

The next id is the next `SC-NN` after the highest id already in the business matrix. Existing ids are not reused or renumbered.

---

## Requirement Change

```text
Changed Requirement
      ↓
revise: Contract Pyramid (rules updated first)
      ↓
revise: Code that enforces the rule
      ↓
List of files touched + rule changed
```

A route is deleted only when the OpenAPI document no longer lists it.

---

# Recommended End-to-End Workflow

For a new service that will keep growing, GUARDED-SKILLS can be used as a complete pipeline:

```text
┌──────────────────────┐
│    Requirements      │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│  Contract Pyramid    │
│  rules, before code  │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│  ArchForge Scaffold  │
│  runnable Go service │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│   Scenario Slice     │
│   one SC-NN at a     │
│        time          │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│ Requirement change   │
│ revise contract,     │
│ then the code        │
└──────────────────────┘
```

Not every task requires every skill.

Scaffold once. Slice whenever behavior is added. Revise the contract whenever a rule changes.

---

# Design Principles

### Rules Before Code

The contract is written first. Code exists to enforce it.

### Contract First on Change

A requirement change updates the contract before it touches the code.

### One Scenario at a Time

New behavior arrives in small slices, never as a bulk rewrite.

### One Place for Each Rule

A rule lives in one layer of the pyramid. It is not copied into a second layer with different wording. Error codes live in one table.

### Never Overwrite Silently

`create` stops when the destination file already exists.

### Prefer Focused Changes

`adjust` edits only what the user named. Everything else stays untouched.

### Keep Changes Traceable

`revise` lists every file touched and the rule that changed.

### Do Not Invent What Was Not Given

Missing transitions, fields, and error codes stop the skill. They are not filled in.

### Verify What Was Written

Scaffold and slice finish by pasting test and build output. A skipped durable-store check is reported as not verified.

---

# Technology Support

GUARDED-SKILLS currently includes one stack-agnostic skill and two skills with a Go profile.

```text
Stack-Agnostic
└── contract-pyramid

Go
├── archforge-scaffold
└── scenario-slice
```

`archforge-scaffold` and `scenario-slice` write Go files from the `go-gin-gorm` profile.

Supporting another stack means adding a new folder under `profiles/`. Without a profile for that stack, the skill stops and asks.

---

# Shared Example

All three skills use one fictional service in their examples.

| Item | Value |
| :--- | :--- |
| Service | Ledger |
| Module | `example.com/ledger` |
| Aggregate | Entry |
| Package | `entry` |
| Collection | `entries` |
| Table | `ledger_entries` |
| Port | `8080` |

---

# Template Tokens

Templates use the tokens below. Replace every token before writing to a target project.

| Token | Meaning |
| :--- | :--- |
| `{{Service}}` | PascalCase service name |
| `{{service}}` | Lowercase service slug |
| `{{module}}` | Go module path |
| `{{Aggregate}}` | PascalCase aggregate name |
| `{{aggregate}}` | Lowercase package name |
| `{{collection}}` | Route segment, plural |
| `{{table}}` | Relational table name |
| `{{port}}` | Listen port |

---

# Repository Structure

```text
guarded-skills/
├── skills/
│   ├── contract-pyramid/
│   ├── archforge-scaffold/
│   └── scenario-slice/
│
├── LICENSE.md
└── README.md
```

Each skill is independently installable while remaining composable with the others.

---

# Philosophy

A coding agent can generate a whole service in minutes. The hard part is making sure it still does what the rules say, and keeps doing so as requirements change.

GUARDED-SKILLS is built around that problem.

```text
Write the rules.
Generate from the rules.
Add one scenario at a time.
When requirements change, change the rules first.
```

> **Don't just generate a service. Guard it.**

---

# Status

GUARDED-SKILLS is under active development.

The current implementation includes one stack-agnostic skill and two skills with a Go profile (`go-gin-gorm`). Additional stacks may be added over time as new profiles.

---

# Contributing

Contributions, improvements, examples, and new stack profiles are welcome.

When adding a new profile, prefer changes that:

1. Keep the shared vocabulary of layers, ports, scenarios, and error codes
2. Produce runnable output
3. Respect the `create`, `adjust`, and `revise` modes
4. Reuse the shared example where possible
5. Stop and ask when a required input or profile is missing

---

# License

See [LICENSE.md](LICENSE.md) for details.
