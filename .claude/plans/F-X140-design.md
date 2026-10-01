# F-X140, Rendering and layout contribution wave

**Status**: completed
**Sprint**: S77
**Size**: L
**Depends on**: F-X137

## Problem

PRs 175, 188, 196, 199, 200, 206 and 207 repair PDF text mapping,
presentation backgrounds and shape construction, and Word pagination or
restart behavior. They overlap on integration tests, archive measurements,
documentation and `crates/rdocx-layout/src/engine.rs`. The current connector
constructor in `crates/rpptx/src/lib.rs:5695` and layout restart cache in
`crates/rdocx-layout/src/engine.rs:890` are among the affected paths.
PR 188 alone updates the hash baseline. PR 196's Presentation fidelity CI
stopped during a pinned LibreOffice download. PR 206's Test and MSRV jobs
failed the same generated deck hash assertion.

## Spec reference

- `docs/hld/05-drawingml-model.md`, "Colour, the part everyone gets wrong",
  "Geometry", "Text body" and "Preservation".
- `docs/hld/06-presentationml-model.md`, "Public facade", "The shape tree"
  and "Validation".
- `docs/hld/07-inheritance-and-resolution.md`, "The output contract" and
  "The resolver".
- `docs/hld/08-rendering-spec.md`, "The PDF backend", "Tables" and "The
  renderer's input".
- `docs/hld/12-testing-strategy.md`, "The hash harness", "The Word render
  fidelity gate" and "The render fidelity gate".

## Approach

Review each of the seven PRs against the completed S76 prefix, then replay
only its unique behavior and tests. Reconcile the two Word layout edits in
one engine, the presentation shape edits in one facade, and the shared
integration test entrypoints. Preserve DrawingML unknown XML and schema
order. Keep the ToUnicode ligature fix and its seven-sample PDF hash delta in
a distinct labelled commit. Repair PR 206's source-built SmartArt fixture
expectation only after inspecting the changed geometry and generated deck.
Rerun PR 196's pinned Presentation fidelity gate with its oracle available.
Remeasure archive sizes once for the combined result.

Existing output behavior changes deliberately. No new public API is needed
beyond the contributed pre-1.0 facade corrections.

## Rejected alternatives

- Record a new baseline for every contribution. The harness is one exclusive
  resource, and only PR 188 declares a PDF delta.
- Treat PR 196's failed download as a render mismatch. The corpus did not run.
- Update PR 206's fixture hash without inspecting the generated deck.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| golden | Deterministic Word and presentation fidelity gates | Layout and slide rendering pass with pinned fonts and oracle. |
| golden | Output stability harness | Exactly the declared seven-sample PDF resource and byte deltas occur. |
| regression | Existing `rdocx-layout` and `rpptx` integration entrypoints | Keep-with-next and restart behavior, picture geometry, connectors, backgrounds and paragraph properties. |
| round-trip | Existing presentation integration cases | Unknown XML survives and authored shape XML respects schema child order. |

**Test gate**: golden, as stated in the backlog. Run focused layout,
presentation, PDF and drawing checks, then the scoped gate.

## HLD impact

- `docs/hld/05-drawingml-model.md`
- `docs/hld/06-presentationml-model.md`
- `docs/hld/07-inheritance-and-resolution.md`
- `docs/hld/08-rendering-spec.md`
- `docs/hld/12-testing-strategy.md`

## Risk routing

- Parser or serialiser: read `docs/hld/04-opc-and-packaging.md` and
  `06-presentationml-model.md`. Check schema child order, prefix-tolerant
  parsing and byte-preserving unmodelled subtree round-trip.
- Layout and text shaping: read `docs/hld/08-rendering-spec.md`. Run every
  baseline and fidelity check in deterministic font mode.
- Theme colour and mapping: read `docs/hld/05-drawingml-model.md`. Leave the
  deliberate Word tint or shade function unchanged and check presentation
  colour results against the render gate.
- External oracle comparison: read `.claude/skills/differential-testing.md`,
  pin the LibreOffice version and record it with fidelity evidence.
- Public API of published crates: read `docs/hld/10-bindings-spec.md`, state
  any public facade semver effect, run `cargo publish --dry-run` for touched
  crates and check `.crate` size.

## Hash harness

PR 188's expected delta is `pdf/resources` and `pdf/bytes` for each of the
seven samples. `pdf/pages`, PNG and Word XML entries remain byte-identical.
The baseline update is its own labelled behavior commit and is reviewed
against the actual ToUnicode ligature output.

## Implementation checklist

- [x] Review and replay the seven PRs against S76, reconciling shared tests
  and source paths.
- [x] Isolate and review PR 188's expected PDF hash baseline change.
- [x] Inspect PR 206's source-built deck and repair its fixture expectation.
- [x] Rerun PR 196's pinned Presentation fidelity gate.
- [x] Run focused checks, risk riders and microscope to zero findings.

## Open questions

None. The failed CI jobs require evidence and repair, not a product policy
decision.
