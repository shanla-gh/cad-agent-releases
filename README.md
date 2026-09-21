# CAD Agent

**AI-powered SOLIDWORKS automation that turns design intent into native, editable parametric CAD.**

![Status: pre-release](https://img.shields.io/badge/status-pre--release-orange)
![Platform: Windows](https://img.shields.io/badge/platform-Windows-0078D6)
![Tested with SOLIDWORKS 2026](https://img.shields.io/badge/tested%20with-SOLIDWORKS%202026-red)

CAD Agent lets you describe a mechanical part — or a change to one — in plain language, and builds it in
SOLIDWORKS as a real parametric model: named dimensions, sketch relations, native features and patterns
that you can open and keep editing by hand afterwards.

It is not a mesh generator and not a macro recorder. Every request becomes a structured, validated plan,
a deterministic engine executes that plan through the SOLIDWORKS API, and the resulting model is
measured and checked before the job is reported as a success.

```
> Create a 120 by 80 by 8 millimeter mounting plate with four 8 millimeter
  through holes, 10 millimeters from the edges, and 4 millimeter corner fillets.

> Make it 140 millimeters long.
```

The second request never mentions the holes. They move anyway — because the model knows they belong
10 mm in from the edges.

---

## Download

> **Public builds are not available yet.**
> **First release: TBA.**

When builds are published they will be distributed exclusively through
[GitHub Releases](https://github.com/shanla-gh/cad-agent-releases/releases) on this repository, together
with release notes and the tested environment for each version. Until then there is nothing to download
from this repository, and no other source is official.

Watch this repository (**Watch → Custom → Releases**) to be notified when the first build is published.

---

## Why CAD Agent

Most CAD time is not spent drawing geometry once. It is spent creating the *same kinds* of parts again
and again, and changing existing models when a requirement moves. Generating geometry is the easy half;
producing a model that stays correct when someone edits it is the hard half.

CAD Agent is built around that second half:

- **Editable output, not frozen geometry.** Parts are built from native SOLIDWORKS sketches, dimensions,
  relations and features, with meaningful names — so the model can be edited later, in SOLIDWORKS or by
  another request.
- **Design intent is preserved.** A hole pattern can be tied to the plate's size, a bolt circle is one
  circular pattern with a count, a pocket can stop at a face instead of at a hard-coded depth.
- **AI proposes; it never executes.** A language model can only propose a plan. The plan is data from a
  fixed operation catalog, it is validated before anything runs, and only a deterministic engine talks to
  SOLIDWORKS.
- **Success is measured, not assumed.** The SOLIDWORKS API sometimes reports success while doing nothing.
  CAD Agent reads the model back — volumes, bounding boxes, parameters, relations, feature types — and a
  job that did not produce the intended part is reported as failed.

---

## How it works

```
 Natural-language request        Existing model context
            │                              │
            └──────────────┬───────────────┘
                           ▼
                Structured design intent
                           ▼
                  CadPlan  (validated)
                           ▼
                  Deterministic executor
                           ▼
          Native SOLIDWORKS parametric features
                           ▼
              Geometric and state verification
```

1. **Understand.** A planner turns the request — and, for a modification, the current state of the open
   model — into design intent: which part, which sizes, which relationships.
2. **Plan.** That intent is expressed as a `CadPlan`: an ordered list of operations from a fixed catalog,
   with typed arguments and semantic names. A plan contains no code.
3. **Validate.** The plan is checked before anything touches SOLIDWORKS: arguments, units, ordering,
   references between operations, file safety, and parameter kinds.
4. **Execute.** A deterministic executor performs each operation through the SOLIDWORKS API and stops at
   the first failure.
5. **Verify.** The produced model is measured and compared with what the plan claimed.

Read more in [How it works](docs/how-it-works.md) and [Architecture](docs/architecture.md).

---

## What works today

Everything in this section has been exercised against **SOLIDWORKS 2026 SP03.2** with the resulting
parts measured. CAD Agent is pre-release software; see [Known limitations](#known-limitations).

| Area | Capabilities |
| --- | --- |
| Planning | Natural-language create and modify requests; a deterministic rule-based planner for plates; an experimental language-model planner that proposes plans for the wider operation catalog |
| Sketching | Centre rectangles, lines, arcs, circles, slots, construction lines; closed multi-segment profiles; face-based sketches |
| Design intent | Named driving dimensions; typed sketch dimensions (length, horizontal, vertical, diameter, radius, angle); native sketch relations; origin anchoring; global variables and linked dimensions |
| Parameters | Typed **Length** (mm), **Angle** (degrees) and **Count** parameters, read back and edited by kind |
| Features | Boss and cut extrudes with blind, mid-plane, through-all (cuts) and up-to-face end conditions; fillet; chamfer; shell; revolve; mirror |
| Patterns and references | Linear and circular patterns with editable counts and angles; semantic reference axes, including axes from cylindrical faces |
| Selection | Guarded edge and face queries that select geometry by what it is, never by index |
| Existing parts | Inspect any SOLIDWORKS part, whoever modelled it; say what a phrase means among its dimensions, or refuse when it could mean more than one; change a dimension, an angle or a pattern count and save the result as a new file; compare two parts and see exactly what differs |
| Models | State inspection, parameter modification, and versioned saving that never overwrites its source |
| Output | SLDPRT and STEP export, inside a dedicated output folder |
| Safety | Plan validation, sandboxed file paths, no silent overwrites, safe document ownership |
| Desktop | Describe a part, preview the plan, build it, edit its parameters, export STEP |

The full, versioned list is in [Supported features](docs/supported-features.md).

---

## Status

| | |
| --- | --- |
| **Implemented** | Everything listed under [What works today](#what-works-today) |
| **In development** | First public build; broader natural-language coverage of the operation catalog; additional face-relative end conditions |
| **Planned** | Assemblies and drawings; additional export formats; automatic SOLIDWORKS start-up; inferring design intent from geometry somebody else modelled |
| **Long-term vision** | Technical-drawing and image-assisted reconstruction; phone scan to **editable parametric CAD**; deeper design-intent inference; engineering workflow integrations |

See the [Roadmap](ROADMAP.md) for detail. Long-term items are direction, not commitments.

---

## Requirements

| | |
| --- | --- |
| Operating system | Windows, 64-bit |
| CAD | SOLIDWORKS, licensed and activated. Development and testing use **SOLIDWORKS 2026 SP03.2**; other versions are not yet verified |
| Runtime | .NET Framework 4.8 |
| Language-model planning | Optional; requires an API credential for the configured provider |

Details in [Requirements](docs/requirements.md).

---

## Known limitations

- SOLIDWORKS must already be running; CAD Agent attaches to the running session.
- Parts only — no assemblies or drawings yet.
- Editing an existing part reaches its **dimensions, relationships and pattern counts**. Adding or
  removing features in a part CAD Agent did not create is not supported, and neither is anything that
  is not a dimension — a material, a mass, a tolerance or an appearance is refused rather than
  attempted.
- A request that could mean more than one dimension is refused, with every candidate named. CAD Agent
  does not choose between them.
- Millimetres only. Requests in other units are refused rather than converted.
- The rule-based planner understands plates; broader natural-language requests rely on the
  experimental language-model planner.
- Up-to-face termination supports planar target faces only.
- Constant-radius fillets only.

More in [Troubleshooting](docs/troubleshooting.md).

---

## Documentation

- [Installation](docs/installation.md)
- [Getting started](docs/getting-started.md)
- [Requirements](docs/requirements.md)
- [How it works](docs/how-it-works.md)
- [Supported features](docs/supported-features.md)
- [Architecture](docs/architecture.md)
- [Troubleshooting](docs/troubleshooting.md)
- [Roadmap](ROADMAP.md) · [Changelog](CHANGELOG.md) · [Security](SECURITY.md) · [Support](SUPPORT.md)

---

## Feedback

- **Bug reports:** [open a bug report](https://github.com/shanla-gh/cad-agent-releases/issues/new?template=bug_report.yml)
- **Feature requests:** [request a feature](https://github.com/shanla-gh/cad-agent-releases/issues/new?template=feature_request.yml)
- **Security issues:** please follow [SECURITY.md](SECURITY.md) instead of opening a public issue.

---

## About this repository

This is the official public product and release repository for CAD Agent: documentation, release notes,
issue tracking and — once available — downloadable builds. CAD Agent itself is proprietary software; its
source code is not published here.

SOLIDWORKS is a trademark of Dassault Systèmes SolidWorks Corporation. CAD Agent is an independent
product and is not affiliated with or endorsed by Dassault Systèmes.

Copyright © 2026 shanla-gh. All rights reserved.
