# GUARDED-SKILLS

**Engineering skills for building services rules-first: write the contract, scaffold a runnable service from it, then extend it one scenario at a time.**

GUARDED-SKILLS is a collection of reusable skills for AI coding agents that helps engineers move from **explicit rules to a running service, and from a running service to new behavior, without drifting away from those rules**.

Instead of generating a service and hoping it matches the requirements, GUARDED-SKILLS enforces a fixed order:

```
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

```
npx skills add rfanazhari/guarded-skills
```

### Install a specific skill

```
npx skills add rfanazhari/guarded-skills --skill contract-pyramid
```

```
npx skills add rfanazhari/guarded-skills --skill salt-scaffold
```

```
npx skills add rfanazhari/guarded-skills --skill scenario-slice
```

### Manual installation

Copy a skill folder into the agent skills directory.

```
cp -r skills/contract-pyramid skills/salt-scaffold skills/scenario-slice <agent-skills-dir>/
```

Restart your AI coding agent after installation so the latest skills are loaded.

---

# Skills

GUARDED-SKILLS currently provides three skills, intended to be used in this order.

## 1. Contract Pyramid

**Skill:** `contract-pyramid`

Writes the rules of the service: the contract that every later step must follow.

The skill is **stack-agnostic**. It speaks only in:

```
Layers
Ports
Scenarios
Error codes
```

### Use for

- Defining the rules of a new service
- Making business and technical rules explicit before code exists
- Changing requirements by updating the contract first

---

## 2. Salt Scaffold

**Skill:** `salt-scaffold`

Writes a runnable Go service from the rules produced by `contract-pyramid`.

It keeps the same vocabulary as the contract. The Go files it writes come from the `go-gin-gorm` profile.

### Use for

- Starting a new service from an existing contract
- Producing a service skeleton that already follows the rules

---

## 3. Scenario Slice

**Skill:** `scenario-slice`

Adds one scenario at a time to a service that already has the rules.

It uses the same vocabulary and the same `go-gin-gorm` profile as `salt-scaffold`.

### Use for

- Extending an existing service by a single scenario
- Keeping every change small, focused, and traceable to a rule

---

# Modes

Each skill supports the same three modes.

| Mode | Behavior |
| :--- | :--- |
| `create` | Writes files that do not exist yet. Stops if the destination file already exists. |
| `adjust` | Edits only the sections the user named. Every other scenario row and file stays untouched. |
| `revise` | Applies a requirement change. Updates the contract first, then the code that enforces it. Lists every file touched and the rule that changed. |

---

# Engineering Workflows

The skills are designed to work in sequence.

## New Service

```
Requirements
      ↓
Contract Pyramid
      ↓
Rules
      ↓
Salt Scaffold
      ↓
Runnable Go Service
```

## New Scenario on an Existing Service

```
Existing Rules + Service
      ↓
Scenario Slice
      ↓
One New Scenario
```

## Requirement Change

```
Changed Requirement
      ↓
revise: Contract Pyramid (rules updated first)
      ↓
revise: Code that enforces the rule
      ↓
List of files touched + rule changed
```

---

# Design Principles

### Rules Before Code

The contract is written first. Code exists to enforce it.

### Contract First on Change

A requirement change updates the contract before it touches the code.

### One Scenario at a Time

New behavior arrives in small slices, never as a bulk rewrite.

### Never Overwrite Silently

`create` stops when the destination file already exists.

### Prefer Focused Changes

`adjust` edits only what the user named. Everything else stays untouched.

### Keep Changes Traceable

`revise` lists every file touched and the rule that changed.

---

# Stack Support

```
Stack-Agnostic
└── contract-pyramid

Go
├── salt-scaffold
└── scenario-slice
```

`salt-scaffold` and `scenario-slice` write Go files from the `go-gin-gorm` profile.

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

```
guarded-skills/
├── skills/
│   ├── contract-pyramid/
│   ├── salt-scaffold/
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

```
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

---

# License

See [LICENSE.md](LICENSE.md) for details.