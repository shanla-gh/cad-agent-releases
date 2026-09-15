# Getting Started

> Public builds are not available yet. This guide describes the workflow of the current development line
> so that you know what to expect from the first release.

## 1. Start SOLIDWORKS

CAD Agent attaches to a running SOLIDWORKS session. Start SOLIDWORKS first and leave it open.

## 2. Connect

Open CAD Agent and connect to SOLIDWORKS. The status shows when the connection is established.

## 3. Describe a part

Write what you want in plain language, with sizes in millimetres. For example:

```
Create a 120 by 80 by 8 millimeter mounting plate with four 8 millimeter
through holes, 10 millimeters from the edges, and 4 millimeter corner fillets.
```

Choose a planner:

- **Rule-based planner** — deterministic. It understands plates: overall size, a four-hole pattern, an
  edge margin and corner radii. It refuses what it does not understand rather than guessing.
- **Language-model planner** — experimental. It can propose plans that use more of the operation catalog,
  and requires an API credential. What it proposes can vary, but it passes through exactly the same
  validation as every other plan.

## 4. Review the plan

Preview the plan before building. A plan lists each operation CAD Agent will perform — sketches,
dimensions, features, patterns, saves — together with any problems the validator found. Nothing has been
built at this point.

## 5. Build it

Build the plan in SOLIDWORKS. CAD Agent executes the operations in order, stops at the first failure, and
then verifies the result: size and position, solid bodies, named parameters and, where the plan states
them, volume, relations and feature types. The result of every check is shown.

A job is reported as successful only when it ran completely and the model passed verification.

## 6. Change it

Changing a part is where CAD Agent differs from a one-shot generator. Either:

- describe the change, for example `Make it 140 millimeters long.`, or
- edit a named parameter's value directly and apply it.

Because the model carries its design intent — for example, holes positioned relative to the plate's
edges — related geometry follows the change. The modified part is saved as a new version; the model it
was opened from is never overwritten.

## 7. Export

Export the part to STEP for manufacturing or exchange. Parts are saved as native SLDPRT files that you can
keep editing in SOLIDWORKS without CAD Agent.

## Tips

- State sizes explicitly and in millimetres.
- Prefer describing relationships ("10 mm from the edges", "evenly around a circle") over coordinates; they
  are what make the model editable.
- If a job fails, read the error code and the verification report first — see
  [Troubleshooting](troubleshooting.md).

## Next steps

- [How it works](how-it-works.md)
- [Supported features](supported-features.md)
