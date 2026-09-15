# Troubleshooting

CAD Agent reports problems with a status, a message and, usually, a structured error code. Start with the code
and the verification report — they say where the job stopped and why.

## Job statuses

| Status | Meaning |
| --- | --- |
| Succeeded and verified | The plan ran completely and the model passed every verification check |
| Succeeded with warnings | The model passed verification, and some operations reported warnings worth reading |
| Execution failed | An operation failed; later operations were skipped |
| Verification failed | The plan ran, but the model is not what the plan described |
| Cancelled | The job was stopped before it finished |

If the plan is **invalid**, no job runs at all: the validation report lists every problem found.

## Connecting to SOLIDWORKS

**CAD Agent cannot connect.**

- Start SOLIDWORKS before connecting; CAD Agent does not start it.
- Wait until SOLIDWORKS has finished starting and no dialogs are open.
- Make sure SOLIDWORKS is licensed and has been started at least once since installation or update.
- Check that you are using a verified SOLIDWORKS version — see [Requirements](requirements.md).

**A modal dialog appears in SOLIDWORKS.** Close it; an open dialog can block API calls until it is dismissed.

## Planning

**"The planner could not express this request."**

- The rule-based planner understands plates. For other parts, try the language-model planner if available.
- State sizes explicitly and in millimetres. Other units are refused rather than converted.
- Describe one part at a time.

**The language-model planner is unavailable.** It needs an API credential for its configured provider and
network access. The rule-based planner works without either.

**A proposal is refused with `SW_PARAMETER_KIND_MISMATCH`.** The plan tried to give a parameter a value of the
wrong kind — for example a pattern count in millimetres. Ask again stating the value in the right unit: lengths
in millimetres, angles in degrees, counts as whole numbers.

## Validation errors

| Code | Usual cause | What to do |
| --- | --- | --- |
| `PLAN_INVALID_ARGUMENT` | A missing, malformed or out-of-range argument | Read the details; they name the argument and the accepted values |
| `PLAN_PRECONDITION_FAILED` | Operations in an order a CAD system cannot perform | For example, cut only after a solid exists; close a sketch before building a feature |
| `PLAN_UNSUPPORTED_OPERATION` | An operation this build does not support | See [Supported features](supported-features.md) |
| `PLAN_UNSAFE_PATH` | A file path outside the output folder, absolute, or with an unexpected extension | Use a relative file name |
| `SW_SKETCH_ENTITY_NOT_FOUND` | A relation or dimension names an entity not drawn in the same sketch | Name the entity where it is drawn |
| `SW_PARAMETER_KIND_MISMATCH` | A length, angle or count given to a parameter of another kind | Use the parameter's own kind |

## Execution errors

| Code | Usual cause | What to do |
| --- | --- | --- |
| `SW_SELECTION_COUNT_MISMATCH` | An edge or face query matched a different number of entities than expected | The error lists what matched; tighten or correct the query |
| `SW_FEATURE_CREATION_FAILED` | SOLIDWORKS refused the feature, or it changed no material | Check sizes against the part: a shell too thick, a cut that misses the solid, a target face behind the sketch |
| `SW_SKETCH_DIMENSION_MISMATCH` | A dimension's value differs from the drawn geometry | Draw the geometry at the stated size; dimensions record sizes, they do not move geometry |
| `SW_SKETCH_ANCHOR_NOT_AT_ORIGIN` | An origin anchor on a point that is not at the origin | Draw that point at (0, 0) |
| `SW_SKETCH_RELATION_REFUSED` / `SW_SKETCH_DIMENSION_REFUSED` | SOLIDWORKS did not add the relation or dimension, often because the sketch is already constrained | Remove the redundant relation or dimension |
| `SW_PARAMETER_NOT_FOUND` | The model has no parameter by that name | Check the names in the model state |
| `SW_PARAMETER_DRIVEN` | The parameter is derived from another one | Change the parameter it is derived from |
| `SW_DOCUMENT_IN_USE` | A document CAD Agent needs is open and was not created by it, or has unsaved changes | Save or close it yourself, or explicitly allow CAD Agent to close open parts |
| `SW_REBUILD_FAILED` | The model has rebuild errors | Inspect the feature tree in SOLIDWORKS |

## Verification failures

A verification failure means the job ran but the model does not match the plan. The report shows each check,
the expected value and the actual value. Common reasons:

- a parameter change produced a different shape than intended because the sketch was not fully constrained;
- a dimension was applied, but SOLIDWORKS resolved the sketch differently than expected;
- a file was not written.

Please report unexpected verification failures — they are exactly the cases CAD Agent is designed to catch.

## Files

**CAD Agent will not overwrite my file.** This is deliberate. Confirm the overwrite explicitly, or save as a new
version.

**My original model did not change after a modification.** Also deliberate: modifications are saved as a new
file, and the source model is never overwritten.

## Still stuck?

See [SUPPORT.md](../SUPPORT.md) and open a [bug report](https://github.com/shanla-gh/cad-agent-releases/issues/new?template=bug_report.yml)
with the version, the request or plan, the job status, error codes and verification results.
