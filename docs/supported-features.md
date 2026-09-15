# Supported Features

This page lists what the current development line of CAD Agent supports. Everything under **Implemented**
has been run against **SOLIDWORKS 2026 SP03.2** and the resulting parts measured. No public build is
available yet; each release's notes will state that build's exact scope.

## Implemented

### Workflows

| Capability | Notes |
| --- | --- |
| Create parts from natural language | Rule-based planner for plates; experimental language-model planner for the wider catalog |
| Modify existing parts | Change named parameters by request or by editing a value; saved as a new version |
| Plan preview and dry run | Review and validate a plan without building anything |
| Plan execution | Deterministic, ordered, stops at the first failure |
| Verification | Every job is checked against the produced model |
| Desktop app | Connect, describe, preview, build, edit parameters, export STEP |
| Command line | Plan, dry-run, execute and modify |

### Documents and output

| Capability | Notes |
| --- | --- |
| New part documents | From the default part template, millimetre units |
| Open, close and save | Ownership tracking: only documents CAD Agent created or opened are closed, never with unsaved changes |
| Save as | New versions instead of overwriting; the source of a modification is never overwritten |
| Rebuild | Normal and forced rebuild |
| STEP export | Written alongside the part in the output folder |
| State inspection | Features and types, parameters and their kinds, variables, relations, sketch definition, bodies, bounding box, volume |

### Sketches

| Capability | Notes |
| --- | --- |
| Sketch on default planes | Front, Top and Right |
| Sketch on a face | Face selected by a guarded face query; must resolve to exactly one planar face |
| Centre rectangle | With named width and height |
| Circle | With named diameter and optional named position from the origin |
| Line, arc, construction line | Named entities that relations and dimensions can refer to |
| Slot | Native straight slot with named width and length |
| Closed profiles | Separately drawn segments joined into one profile |

### Design intent

| Capability | Notes |
| --- | --- |
| Named dimensions | Semantic names such as `CA_PLATE_LENGTH` on sketch and feature dimensions |
| Sketch dimensions | Length, horizontal, vertical, diameter, radius and angle, on named entities; recorded at the drawn size, read back and never used to move geometry |
| Sketch relations | Horizontal, vertical, coincident, concentric, equal, symmetric, parallel, perpendicular, tangent |
| Origin anchoring | Line start, end or midpoint, and circle or arc centre, held at the sketch origin |
| Variables and derived dimensions | Global variables, and dimensions linked to other dimensions |
| Typed parameters | Length (mm), Angle (degrees), Count; edited by kind, never converted between kinds |
| Sketch definition status | Under, fully or over defined, reported for inspection |

### Features

| Feature | Supported options |
| --- | --- |
| Boss extrude | Blind, mid-plane, up to a face; direction; draft; named depth |
| Cut extrude | Blind, mid-plane, through all, up to a face; direction; named depth |
| Fillet | Constant radius on a guarded edge selection; named radius |
| Chamfer | Equal distance on a guarded edge selection; named distance |
| Shell | Wall thickness with open faces chosen by a guarded face query; named thickness |
| Revolve | About a named reference axis; full or partial angle; named angle |
| Mirror | A named feature about a reference plane |
| Linear pattern | Along a guarded edge direction; named spacing and count |
| Circular pattern | About a named axis; equal or step spacing; named count and angle |

### References and selection

| Capability | Notes |
| --- | --- |
| Reference axes | From two planes, or from a cylindrical face |
| Guarded edge queries | By geometry, direction, adjacent surfaces, length, radius and position, with a required expected count |
| Guarded face queries | By geometry (planar, cylindrical), normal or axis, radius, area, position and source feature, with a required expected count |
| Face inspection | Measure what a face query matches without changing the model |

### Safety and verification

| Capability | Notes |
| --- | --- |
| Plan validation | Arguments, ordering, references, selection counts, parameter kinds, file paths |
| File sandbox | Relative paths inside a dedicated output folder only |
| No silent overwrite | Replacing a file requires explicit confirmation |
| Silent no-op detection | Features that report success without changing the model are reported as failures |
| Verification checks | Size, position, body count, volume, parameters by kind, relations, feature types, sketch definition, files written |

## Current limits of implemented features

- Parts only; no assemblies or drawings.
- Millimetres only.
- SOLIDWORKS must already be running.
- Modification targets models created by CAD Agent (they carry its semantic names). Other models can be
  inspected but not yet edited.
- Up-to-face termination requires a planar target face; offset and curved targets are not supported.
- Fillets are constant-radius; chamfers are equal-distance.
- Angle parameters cannot yet be derived from one another.
- The rule-based planner covers the plate family; broader natural-language coverage depends on the
  experimental language-model planner.

## In development

- Broader, more reliable natural-language coverage of the operation catalog.
- Relationships between angle parameters.
- Offset-from-face and curved-face termination.
- Packaging for the first public build.

## Planned

- Understanding and editing parts that CAD Agent did not create.
- Assemblies and drawings.
- STL and other export formats.
- Draft, variable-radius fillets and multi-direction patterns.
- Automatic SOLIDWORKS start-up.
- Inch-based requests.

## Long-term vision

- Technical-drawing and image-assisted reconstruction.
- Phone scan to editable parametric CAD — sketches, dimensions, relations and features, not just a mesh.
- Deeper design-intent inference.
- Engineering workflow integrations.

These are not implemented. See the [Roadmap](../ROADMAP.md).
