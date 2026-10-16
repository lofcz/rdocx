# F-X147, Complete rdocx Python production checklist

**Status**: completed
**Sprint**: S80
**Size**: L
**Depends on**: F-X141, F-X145, F-X153

## Problem

Issue 168 asks for a complete Python editing chain, but the integrated tests in `crates/rdocx-py/tests/test_core.py` exercise its operations separately. The binding already exposes counted replacement, section stories, style creation and removal, run removal, picture resizing and text-anchored comments in `crates/rdocx-py/src/document.rs`. There is no single save, reopen, layout and render acceptance case. Rich footer construction currently relies on whole-story text calls, so a mixed run, tab and field workflow needs an explicit check and possibly a binding repair. Arbitrary package part replacement also needs a scope decision before exposing a write path.

## Spec reference

- `docs/hld/03-architecture.md`, "Facade conventions".
- `docs/hld/04-opc-and-packaging.md`, "Package integrity".
- `docs/hld/10-bindings-spec.md`, "The PyO3 lifetime problem", "Python API shape" and "Native Word facade stability".
- `docs/hld/12-testing-strategy.md`, "Binding tests" and "The Word corpus".
- `docs/hld/14-development-backlog.md`, "F-X147, Complete rdocx Python production checklist".

## Approach

Build one Issue 168 acceptance matrix from the issue's exact examples. Run it against the current Python binding and distinguish operations that already pass from actual gaps. Add the minimum binding or native facade repair for each gap, using the existing ownership, revision and atomic publication rules. Exercise a rich section footer with separate run text, tab and PAGE or NUMPAGES fields, and a three-pair counted replacement whose mismatched pair leaves the document bytes unchanged. Check style mutation and removal, hyperlink retarget and unwrap, picture resize, the documented lxml package-write fallback and text-anchored comment after save and reopen. Record any intentionally unsupported long-tail item with its documented fallback and acceptance decision. The reporter explicitly permits a thin lxml step for out-of-scope operations. Keep arbitrary package-part writes outside the typed binding in this story.

## Rejected alternatives

- Treating individually passing binding tests as the production chain would miss owner invalidation and save or render interactions.
- Passing unvalidated raw XML directly through the Python binding could publish broken relationship or content-type state.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| integration | Issue 168 Python chain in the existing `rdocx-py` test entrypoint | Table, style, section, bookmark, field, rich footer, replacement, paragraph, hyperlink, picture, XML and comment steps work together after save and reopen, or name an accepted fallback. |
| regression | Three-pair counted replacement | A wrong expected count names its pair and leaves the document and held handles unchanged. |
| regression | Mixed footer and layout check | One section footer retains a run, tab and PAGE or NUMPAGES fields and resolves them in layout. |
| differential | Pinned python-docx reader for the output | Structural content and authored relationships remain readable at the pinned oracle version. |
| gate | Backlog integration test gate | Every Issue 168 checklist example runs from Python or has an explicit accepted scope decision and documented fallback. |

## HLD impact

- `docs/hld/10-bindings-spec.md`
- `docs/hld/12-testing-strategy.md`

## Risk routing

- Parser or serialiser if the acceptance work finds an XML repair: read `docs/hld/04-opc-and-packaging.md` and `06-presentationml-model.md`. Check schema child order and byte preservation of unmodelled XML.
- Public API of a published crate if the native facade changes: read `docs/hld/10-bindings-spec.md` and `CLAUDE.md` structural rules. State additive pre-1.0 semver impact, run `cargo publish --dry-run` for touched crates and assert `.crate` size.
- PyO3 binding: read `docs/hld/10-bindings-spec.md`. Run binding tests and `cargo check --target wasm32-unknown-unknown -p rdocx-wasm -p rpptx-wasm`. Exclude both Python binding crates from workspace tests.
- Layout and rendering: read `docs/hld/08-rendering-spec.md`. Use deterministic bundled fonts for any render baseline.
- External oracle: read `.claude/skills/differential-testing.md`. Pin and record python-docx version and compare parsed structure.

## Hash harness

Expected unchanged. A code repair that changes a harness input must be declared in a separate labelled behaviour commit before any baseline review.

## Implementation checklist

- [x] Run every Issue 168 example against the integrated S79 binding and record a per-item result.
- [x] Add failing cases for the remaining production-chain gaps to existing test entrypoints.
- [x] Implement only the binding or native changes required by those cases.
- [x] Save, reopen, validate, lay out and render the complete chain with deterministic fonts.
- [x] Record explicit accepted scope decisions and fallbacks for intentionally unsupported items.
- [x] Run focused checks, scoped verification and microscope to zero findings.

## Acceptance results

| Issue 168 item | Result |
|---|---|
| Body-index table insertion, horizontal and vertical merge | Passed in `test_issue_168_complete_edit_save_reopen_layout_and_render`. |
| Table borders, shading, margins, widths and row controls | Passed in the chain and existing table formatting tests. |
| Style create, apply, update, remove and default | Passed in the chain. Python `set_style` was the missing operation and changes rendered pixels. |
| Numbering definition, instance and style link | Passed in the chain and existing numbering tests. |
| Section margins and orientation in layout | Passed in the chain and existing section layout tests. |
| Bookmark range, PAGEREF and layout-backed fields | Passed in the chain and existing field tests. |
| Rich per-section footer with run text, tab and page fields | Passed in the chain after moving a typed body paragraph into the section footer. |
| Expected-count replacement and atomic three-pair replacement | Passed in the chain, including byte identity and live handles on a mismatched pair. |
| PDF with a font directory | Passed with the bundled deterministic fonts. |
| Paragraph text and run removal | Passed in the chain and existing removal tests. |
| Hyperlink retarget and removal | Passed in the chain and existing relationship tests. |
| Existing picture resize | Passed in the chain and existing picture tests. |
| Arbitrary XML or package write | Accepted external lxml ZIP fallback, exercised on `docProps/app.xml` and documented in the Python README. No typed arbitrary-part writer. |
| Text-anchored comment | Passed in the chain and after reopen. |

The pinned `python-docx==1.2.0` reader opened the final package and confirmed
authored paragraphs, relationships, table and footer content. The complete
Python binding suite passed with the reviewed Poppler 26.01.0 oracle.

## Open questions

None. Issue 168 explicitly permits a thin lxml step for out-of-scope operations, so arbitrary package-part writes retain that documented fallback.
