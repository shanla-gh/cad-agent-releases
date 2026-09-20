# How It Works

CAD Agent turns intent into a SOLIDWORKS model in five stages. The important idea is the boundary in the
middle: everything before the plan is *understanding*, everything after it is *deterministic engineering*,
and the only thing that crosses from one to the other is a validated plan.

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

## 1. Understanding the request

A **planner** reads what you asked for. For a modification it also receives the current state of the open
model: its features, its named parameters with their kinds and values, and its relations.

There are two planners:

- a **deterministic rule-based planner**, which reads a defined family of requests (plates, their holes,
  margins and fillets, and changes to them) and refuses anything else;
- an **experimental language-model planner**, which can propose plans using more of the operation catalog.

Both produce the same thing: a plan. Neither can touch SOLIDWORKS.

## 2. Design intent

A good CAD model records *why* geometry is where it is, not only where it is. CAD Agent plans in those terms:

- **Named dimensions** such as `CA_PLATE_LENGTH`, rather than SOLIDWORKS' positional `D1`, so a later change
  can target them reliably.
- **Relationships** — a hole's position derived from the plate's size, symmetric or tangent sketch geometry,
  a profile anchored at the origin — so related geometry follows an edit.
- **Native patterns** — a bolt circle is one circular pattern with a count, not six separate holes.
- **Face-relative features** — a pocket that stops at a shelf floor follows that floor when the shelf moves.
- **Typed parameters** — every parameter is a **Length** in millimetres, an **Angle** in degrees or a
  **Count** of instances. A value is never converted from one kind to another, so "set the six-hole pattern
  to 6 mm" is refused instead of silently producing nonsense.

## 3. The plan

A `CadPlan` is structured data: an ordered list of operations from a fixed catalog, each with typed
arguments, followed by what the finished model is expected to look like. An illustrative fragment:

```json
{
  "operations": [
    { "operationId": "op-010", "type": "sketch.begin_on_face",
      "arguments": { "selection": { "geometry": "planar", "normal": "+z", "expectedCount": 1 } } },
    { "operationId": "op-011", "type": "sketch.create_circle",
      "arguments": { "centerX": 25, "centerY": 0, "diameter": 10,
                     "diameterDimensionName": "CA_HOLE_DIAMETER" } },
    { "operationId": "op-012", "type": "sketch.end",
      "arguments": { "sketchName": "CA_HOLE_SKETCH" } },
    { "operationId": "op-013", "type": "feature.cut_extrude",
      "arguments": { "endCondition": "up_to_face",
                     "targetFace": { "geometry": "planar", "normal": "+z",
                                     "sourceFeature": "CA_SHELF", "expectedCount": 1 },
                     "featureName": "CA_SHELF_LEVEL_HOLE" } }
  ],
  "verification": {
    "expectedSolidBodyCount": 1,
    "expectedParametersMm": { "CA_HOLE_DIAMETER": 10 }
  }
}
```

There is no operation that runs a command, script or macro, and no argument that contains code.

## 4. Validation

Before anything reaches SOLIDWORKS, the plan is validated as a whole:

- every operation exists in the catalog and has valid, finite, in-range arguments;
- operations are in an order a CAD system can perform — a sketch is open before geometry is added, a solid
  exists before it is cut, a profile is available before it is extruded;
- names refer to things the plan actually creates, in the scope where they exist;
- selections state how many entities they expect;
- parameter changes use the right kind;
- file paths are relative and stay inside the output folder.

A plan with errors does not run. All problems are reported at once.

## 5. Deterministic execution

The executor performs each operation in order through the SOLIDWORKS API, using a SOLIDWORKS session that is
already running. The same plan always leads to the same sequence of API calls. Execution stops at the first
failure, and the failure carries a structured error code.

Geometry is **selected by what it is, never by index**. A fillet does not say "edge 3"; it says, for example,
*straight edges parallel to Z between two planar faces, expected count 4*. If the model does not contain
exactly that, nothing is selected and the operation fails — because an edge index that silently points at
different geometry after an upstream change produces a part that rebuilds cleanly and is wrong.

## 6. Verification

The SOLIDWORKS API can report success while doing nothing: a shell that is too thick, a boss that adds no
material, a relation it does not accept, a dimension it quietly makes a reference dimension. CAD Agent does
not trust return values alone. It checks the model it actually produced:

- that expected features exist, and are the expected feature types;
- bounding-box size and position;
- solid body count and volume, including before-and-after volume for material-changing features;
- named parameters, read back by kind;
- sketch relations and how completely sketches are defined;
- where faces ended up, through the same guarded face queries used for selection;
- that saved and exported files exist.

A job that ran but failed verification is reported as such — never as a success.

## Modifying an existing model

Modification follows the same path. CAD Agent opens the model, reads its state, plans the change against
its named parameters, validates, executes, rebuilds and verifies — and saves the result as a new version so
the model it started from is untouched.

### A part CAD Agent did not create

A part somebody else modelled brings two problems with it, and both are solved before anything is
changed.

Its dimensions have names the CAD system chose — `D1`, `D2`, and `D1` again in the next sketch — so a
request cannot simply be matched against a name. CAD Agent reads the part first and works out which
dimension a phrase such as "the hole diameter" means, using what the model reports: what each
parameter measures, what kind of quantity it is, and which feature owns it. If the words fit exactly
one dimension, that is the one changed. If they fit two, the request is **refused** and both are
named with their current values. Choosing between them is not CAD Agent's decision to make, and a
part that is quietly wrong is worse than a question.

Then the edit itself never touches the file it read. The change is written to a new file, that file
is reopened from disk and checked for the value that was asked for, and the original is verified to
be byte-for-byte what it was before. Three separate answers to three separate questions: did the
change happen, did it survive being saved, and is the part you started with still intact.

## Next

- [Architecture](architecture.md) — the components and the boundaries between them
- [Supported features](supported-features.md)
