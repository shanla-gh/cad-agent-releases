# Architecture

This page describes CAD Agent's architecture at the level useful to users, evaluators and integrators. It
explains the components, the boundaries between them, and why the system is built this way. CAD Agent is
proprietary software, and this page does not describe its source code.

## Overview

```
┌────────────────────────── Input ──────────────────────────┐
│  Natural-language request      Existing model context      │
└──────────────────────────────┬─────────────────────────────┘
                               ▼
┌──────────────────────── Planning ─────────────────────────┐
│  Rule-based planner         Language-model planner          │
│              → structured design intent → CadPlan           │
└──────────────────────────────┬─────────────────────────────┘
                               ▼  the only thing that crosses
┌──────────────────────── Validation ───────────────────────┐
│  Operation catalog · arguments · ordering · references      │
│  selection counts · parameter kinds · file-path sandbox     │
└──────────────────────────────┬─────────────────────────────┘
                               ▼
┌──────────────────── Deterministic execution ──────────────┐
│  Operation handlers → SOLIDWORKS adapter → SOLIDWORKS API   │
└──────────────────────────────┬─────────────────────────────┘
                               ▼
┌──────────────────────── Verification ─────────────────────┐
│  Model state read-back · geometry · parameters · relations  │
└────────────────────────────────────────────────────────────┘
```

## Components

### Planners

A planner receives a request — the user's words, and for a modification, a snapshot of the model's current
state — and returns either a plan or a clear refusal. Planners have no access to SOLIDWORKS, the file system
or the network beyond their own service; they cannot execute anything.

- The **rule-based planner** is deterministic: identical requests produce identical plans.
- The **language-model planner** uses a language model to propose plans for a broader range of requests.
  Its output is parsed as data and treated exactly like a hand-written plan. When a model is being modified,
  it is shown each parameter with its kind, and a proposal that gives a parameter a value of the wrong kind
  is refused rather than repaired.

### The CadPlan

The plan is the contract between understanding and execution. It is a versioned, structured document:

- an ordered list of **operations**, each from a fixed catalog;
- **typed arguments** — strings, numbers, booleans and structured objects such as selection queries;
- **semantic names** for sketches, features, dimensions and axes;
- an optional **verification block** stating what the finished model should look like.

Because a plan is data, it can be reviewed before it runs, stored, compared and re-run.

### Validator

The validator checks the whole plan before execution: that operations exist and their arguments are valid;
that the sequence is one a CAD system can perform; that every name refers to something the plan creates in
the right scope; that selections declare expected counts; that parameter changes use the right kind; and
that every file path stays inside the output sandbox. It reports every problem at once, and nothing runs
while any remain.

### Executor and operation handlers

The executor runs a validated plan operation by operation. Each operation has a handler that checks its
runtime preconditions against the live session — a validator can only reason about the plan, while the
handler sees what SOLIDWORKS actually holds — and then performs it. Execution stops at the first failure,
which is reported with a structured error code.

### SOLIDWORKS adapter

The adapter is the only component that talks to SOLIDWORKS. It attaches to a running session, performs each
operation through the SOLIDWORKS API, and returns plain results — never live CAD objects — to the rest of the
system. It is also where public units become internal ones: millimetres and degrees in the plan, metres and
radians inside SOLIDWORKS, converted in one place.

Every API behaviour the adapter relies on has been measured against a real SOLIDWORKS session before being
built into the product, including the cases where the API reports success without doing anything.

### Verification

After execution, CAD Agent reads the model back and compares it with what was expected: features and their
types, size and position, solid bodies and volume, named parameters by kind, sketch relations and definition,
face positions, and written files. The job's final status reflects the verification, not just whether the API
calls returned.

### Document and file safety

Documents and files are handled conservatively: output goes only to a dedicated folder, existing files are
never replaced without explicit confirmation, a modification never overwrites its source, and only documents
CAD Agent created or opened are closed — never with unsaved changes.

## Design principles

**AI does not execute CAD code.** A language model's authority ends at proposing a plan. There is no path from
model output to an API call that does not pass through the plan format and the validator, and the catalog
contains no operation that runs arbitrary code.

**Plans are structured and validated.** A mistake — whether made by a model, a rule, or a person writing a plan
by hand — is caught in one place, with the same rules and the same errors.

**Output is native and editable.** CAD Agent creates SOLIDWORKS sketches, dimensions, relations, features and
patterns — the same things a designer would create — so the result remains a normal, editable SOLIDWORKS part.

**Geometry is selected by what it is.** Edges and faces are chosen by geometric queries with required counts,
never by topology indices that change when the model changes. An ambiguous or missing match fails.

**API success is not CAD success.** Where SOLIDWORKS can report success without producing the intended result,
CAD Agent measures the model and reports the failure.

**Design intent is preserved where supported.** Named dimensions, relations, origin anchors, patterns,
face-relative features and typed parameters are kept in the model so that later edits — by hand or by request —
behave as a designer would expect.

**Honest refusal over plausible guesses.** A capability that has not been verified is refused rather than
approximated. A part that rebuilds cleanly and is wrong is the worst possible outcome.

## Extensibility

Planning is separated from building. New kinds of input — an existing model's geometry, a technical drawing,
an image, a scan — are intended to arrive as new ways of producing design intent and plans, while validation,
execution and verification stay the same. These inputs are on the [Roadmap](../ROADMAP.md) as future work and
are not implemented today.
