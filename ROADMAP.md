# Roadmap

This roadmap describes direction, not dates. Items move between sections as work is verified; nothing is
listed as implemented until it has been run against a real SOLIDWORKS session and the resulting model has
been measured.

## Implemented

The current development line supports native SOLIDWORKS part automation from validated plans:
natural-language create and modify workflows, semantic and typed parameters, sketch relations and origin
anchoring, extrudes and cuts with blind, mid-plane, through-all and up-to-face end conditions, fillets,
chamfers, shells, revolves, mirrors, linear and circular patterns, semantic axes, guarded edge and face
selection, face-based sketches, model state inspection, safe document handling, STEP export, and
verification of every job.

See [Supported features](docs/supported-features.md) for the full list and its limits.

## In development

- **First public build.** Packaging, installation and release notes for a downloadable build published
  through GitHub Releases.
- **Broader natural-language coverage.** Planning that uses more of the operation catalog reliably from
  plain-language requests, not only the plate family covered by the deterministic planner.
- **Richer angle relationships.** Deriving angle parameters from one another, as lengths already can be.
- **More face-relative termination.** Offset-from-face and curved target faces, where they can be
  verified to behave predictably.

## Planned

- **Understanding existing models.** Reading parts that CAD Agent did not create, recognising their
  features and dimensions, and making them safely editable.
- **Assemblies and drawings.** Beyond single parts.
- **Additional export formats**, such as STL.
- **Additional feature vocabulary**, for example draft, variable-radius fillets and multi-direction
  patterns.
- **Automated SOLIDWORKS start-up**, so a running session is no longer a prerequisite.
- **Inch-based requests**, converted explicitly and verifiably.

## Long-term vision

CAD Agent's architecture separates *understanding what a part should be* from *building it*. That makes
new kinds of input possible without changing how parts are built and verified.

- **Deeper understanding and editing of arbitrary SOLIDWORKS models**, including inferring the design
  intent behind geometry someone else modelled.
- **Technical-drawing and image-assisted reconstruction**, turning a drawing or photograph into a
  parametric part with its dimensions and relations.
- **Phone scan to editable parametric CAD.** A scan of a physical object becoming sketches, dimensions,
  relations, axes, patterns and native features — **editable parametric CAD, not just a mesh**.
- **Richer design-intent inference**: symmetry, patterns, typical engineering relationships and the
  parameters a designer would actually want to change.
- **Engineering workflow integrations** with the tools around CAD.

> None of the long-term items are implemented today, and no timeline is promised. Scan-to-CAD, image and
> drawing reconstruction, and computer vision are future work.

## Suggesting priorities

Feature requests help decide what comes next. Please use the
[feature request form](https://github.com/shanla-gh/cad-agent-releases/issues/new?template=feature_request.yml)
and describe the part or workflow you have in mind.
