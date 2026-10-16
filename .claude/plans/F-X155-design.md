# F-X155, Presentation drawing API contribution

**Status**: completed
**Sprint**: S80
**Size**: L
**Depends on**: F-X142, F-X154

## Problem

Issue 217's drawing checklist cannot yet be authored through the Python facade. PRs 219, 221, 224, 230 and 234 overlap in `crates/rpptx-py/src/shape.rs`, the stubs, the `rpptx` facade and integration tests. PR 219's current head contains only slide-jump behaviour after the existing shape hyperlink work. PR 234's current head builds on the S77 connector style and supersedes the older sprint-plan head. Their shape and connector children need schema-order reconciliation on the F-X154 prefix.

## Spec reference

- `docs/hld/05-drawingml-model.md`, "Geometry", "Text body", "Preservation" and shape properties child order.
- `docs/hld/06-presentationml-model.md`, "Public facade", "The shape tree", "Preservation strategy" and "Validation".
- `docs/hld/07-inheritance-and-resolution.md`, "4. Style references" and "The resolver".
- `docs/hld/10-bindings-spec.md`, "Python API shape".
- `docs/hld/12-testing-strategy.md`, "The deck corpus" and "Binding tests".
- `docs/hld/14-development-backlog.md`, "F-X155, Presentation drawing API contribution".

## Approach

Review each assigned PR's incremental diff and replay its accepted behaviour in order on the completed F-X154 prefix. Extend the existing click action with same-presentation slide jumps and safe target deletion. Expose line dash and end settings, direct outer shadow settings, auto shape preset read and replacement, and a theme effect index that can turn off connector shadow while retaining its line style. Reconcile the shared Python shape handles, facade, OXML model, renderer and stubs once, preserving unknown XML and ordering fill, line, effects, 3-D and extension children by schema. Keep slide import and scoped replacement assigned to F-X156. Use atomic refusal for invalid or stale handles and validate saved packages.

## Rejected alternatives

- Cherry-picking all PR heads in sequence would replay stale parents, duplicate existing click links and overwrite shared binding changes.
- An empty direct effect list alone does not suppress a connector theme shadow in LibreOffice, so use the effect reference index contract from PR 234.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| integration | Existing `rpptx` integration entrypoint for all five PR operations | Links and jumps, line ends, shadow, preset changes and theme effect index retain their intended XML, validate and reopen. |
| regression | Relationship and schema-order cases | Slide target removal prunes only owned relationships, and changed drawing children remain in schema order with raw siblings intact. |
| integration | Existing `rpptx-py` suite and typing smoke | Python handles, enum values and stubs agree with native mutations after save and reopen. |
| differential | Pinned python-pptx 1.0.2 reader | Authored effects, links and geometry have equivalent parsed structure, with documented intentional API differences. |
| golden | Cross-viewer drawing probe | Deterministic rpptx render matches pinned LibreOffice 26.2.5.2 within a declared tolerance. PowerPoint 16.104 remains unverified under the S80 scope decision. |
| gate | Backlog integration test gate | Authored effects and links reopen in python-pptx, validate and match the pinned LibreOffice versus deterministic rpptx render within the declared tolerance. |

The authored deck package SHA-256 is `5a612c739359ea8f56ff18f797b9df38f56440fedaf64f756d31760516b91344`. The native and LibreOffice 26.2.5.2 renders are 2001 by 1125 pixels at 150 DPI. At least 99.7% of pixels must have a maximum RGB channel difference of 12 or less, and each channel's mean absolute error must be 0.2 or less on the 0 to 255 scale. Observed values were 99.790149% and 0.150657, 0.146659 and 0.141661. Python-pptx 1.0.2 reopened the deck and confirmed the authored geometry and slide target. PowerPoint 16.104 was not available for a trustworthy check.

## HLD impact

- `docs/hld/02-scope-and-non-goals.md`
- `docs/hld/05-drawingml-model.md`
- `docs/hld/06-presentationml-model.md`
- `docs/hld/07-inheritance-and-resolution.md`
- `docs/hld/10-bindings-spec.md`

## Risk routing

- Parser and serialiser: read `docs/hld/04-opc-and-packaging.md` and `06-presentationml-model.md`. Check schema order, prefix-tolerant read, fixed-prefix write and byte-preserving retained children.
- Theme colour and effects: read `docs/hld/05-drawingml-model.md`. Keep Word's deliberately naive tint function unchanged and test authored effect resolution separately.
- Layout and rendering: read `docs/hld/08-rendering-spec.md`. Use deterministic bundled fonts for any render baseline.
- Public API of published crates: read `docs/hld/10-bindings-spec.md` and `CLAUDE.md` structural rules. State additive pre-1.0 semver impact, run `cargo publish --dry-run` and assert `.crate` sizes.
- PyO3 bindings: read `docs/hld/10-bindings-spec.md`. Run binding tests and `cargo check --target wasm32-unknown-unknown -p rdocx-wasm -p rpptx-wasm` while excluding binding crates from workspace tests.
- External oracle: read `.claude/skills/differential-testing.md`. Pin python-pptx and LibreOffice versions and compare structures and rendered pixels with explicit tolerances.

## Hash harness

Expected unchanged. Any intentional harness change needs its own labelled behavioural commit and reviewed expected delta.

## Implementation checklist

- [x] Review PRs 219, 221, 224, 230 and 234 against their current bases and the F-X154 prefix.
- [x] Add failing cases for slide jumps, line ends, shadows, geometry and effect index to existing test entrypoints.
- [x] Reconcile OXML, facade, renderer, Python handles and stubs in dependency order.
- [x] Validate, reopen and cross-render the authored drawing deck.
- [x] Run focused checks, scoped verification and microscope to zero findings.

## Open questions

Issue 217 assigns slide import and scoped replacement to F-X156, and the five PRs define this story's authoring slice. For S80 closure, the user accepted pinned LibreOffice 26.2.5.2 versus deterministic rpptx rendering and python-pptx reopen evidence while PowerPoint 16.104 remains explicitly unverified. The installed PowerPoint is 16.113.3 and its AppleScript version query did not return. This scope decision does not claim a PowerPoint pass.
