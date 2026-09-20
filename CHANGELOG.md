# Changelog

All notable changes to CAD Agent's public releases are recorded here.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/). Version numbers will follow
[Semantic Versioning](https://semver.org/) once public builds begin.

## [Unreleased]

No public build has been released yet. The first release date is to be announced.

The capabilities below are implemented in the current development line and verified against
SOLIDWORKS 2026 SP03.2. They describe what the first public build is expected to contain; the release
notes for that build will state its exact scope and tested environment.

### Planning and workflow

- Natural-language create and modify requests.
- Deterministic rule-based planner for plates and changes to them.
- Experimental language-model planner that proposes validated plans for the wider operation catalog, and
  is shown each parameter of an existing model together with its kind.
- Structured, validated `CadPlan` execution with deterministic ordering and first-failure stop.
- Desktop workflow: describe, preview the plan, build, edit parameters, export STEP.
- Command-line workflow for planning, dry runs and plan execution.

### Sketching and design intent

- Centre rectangles, lines, arcs, circles, slots and construction lines; closed multi-segment profiles.
- Sketches started on faces of the part, selected by a guarded face query.
- Named driving dimensions and typed sketch dimensions: length, horizontal, vertical, diameter, radius
  and angle.
- Native sketch relations: horizontal, vertical, coincident, concentric, equal, symmetric, parallel,
  perpendicular and tangent.
- Origin anchoring of line endpoints, midpoints and circle or arc centres.
- Global variables and dimensions derived from other dimensions.
- Typed relationships between parameters: a length derived from a length, an angle derived from an
  angle, with the offset in that kind's own unit. Deriving one kind from another is refused.
- Sketch definition status (under, fully or over defined) reported for inspection.

### Features

- Boss extrude with blind, mid-plane and up-to-face end conditions.
- Cut extrude with blind, mid-plane, through-all and up-to-face end conditions.
- Constant-radius fillet, equal-distance chamfer, shell, revolve and mirror.
- Linear and circular patterns, with editable instance counts and pattern angles.
- Semantic reference axes from two planes or from a cylindrical face.

### Parameters and modification

- Typed Length (mm), Angle (degrees) and Count parameters; values are never converted between kinds.
- Parameter modification, saved as new versions.
- Model state inspection: features, parameters and their kinds, relations, bodies, bounding box and
  volume.

### Working with parts CAD Agent did not create

- Inspection of any SOLIDWORKS part: features classified by what they are, sketches attributed to the
  features that consume them, every parameter with its kind and the name a request must use, and a
  list of what could not be established. Saved to JSON for later comparison.
- Deterministic resolution of a plain-language phrase against the model: "the hole diameter" becomes
  one dimension, or a refusal that names every candidate with its current value.
- Requests naming something no operation can change - a material, a mass, a tolerance, an appearance —
  are identified as such rather than reported as missing.
- Editing dimensions, angles and pattern counts in an existing part: the result is written to a new
  file, reopened from disk and checked, and the original is verified byte-for-byte unchanged.
- Planning a change to an existing part without a language model, from the model's own parameters.

### Selection, verification and safety

- Guarded edge and face queries with required expected counts; ambiguous or missing matches fail.
- Post-execution verification of size, position, body count, volume, parameters, relations, feature
  types and sketch definition.
- Detection of SOLIDWORKS operations that report success without changing the model.
- Sandboxed output paths, no silent overwrites, and document ownership tracking.
- A request that could mean more than one dimension is refused rather than resolved on the user's
  behalf, whether it came from a person or from a language model.
- SLDPRT save and STEP export.
