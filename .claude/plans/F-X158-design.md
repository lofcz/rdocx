# F-X158, Presentation fixture and workflow acceptance gate

**Status**: completed
**Sprint**: S82
**Size**: L
**Depends on**: F-X140, F-X148, F-X154, F-X157

## Problem

`crates/rpptx-py/tests/test_documented_examples.py:4071` and line 4237 build complete Issue 169 and Issue 217 decks, but neither uses the reporter's exported deck. The current suite therefore does not establish the S82 attachment-specific round trip across Issues 169, 170, 215, 216 and 217.

## Spec reference

- `docs/hld/04-opc-and-packaging.md`, "Package integrity".
- `docs/hld/06-presentationml-model.md`, "Preservation strategy", "Relationship remapping" and "Validation".
- `docs/hld/08-rendering-spec.md`, "The renderer's input".
- `docs/hld/10-bindings-spec.md`, "Python API shape".
- `docs/hld/12-testing-strategy.md`, "The deck corpus" and "Binding tests".
- `docs/hld/14-development-backlog.md`, "F-X158, Presentation fixture and workflow acceptance gate".

## Approach

Extend the existing Python deck acceptance entrypoint to run the reporter's seven-slide 4:3 fixture through its scripted edits, save, reopen and `rpptx validate`. Compare normalized trees with pinned `python-pptx==1.0.2` and 72 DPI deterministic slides with pinned LibreOffice 26.2.5.2 using the declared per-window pixel tolerances. Check gradient background and unmodelled text body preservation in the same package. Fetch the original attachment into ignored test storage with SHA-256 `8b703c862792470d3732c6eea07d280d3023525f8653cee4c12d9fc9d14c464a`, or use a local file with that digest. Do not check in binary fixtures.

## Rejected alternatives

- Separate Issue 169 and Issue 217 cases do not prove interactions in the reporter's exported deck.
- A visual comparison alone cannot prove notes, relationships or omitted XML attributes survive.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| differential | `test_issue_158_deck_fixture_acceptance` in the existing Python deck entrypoint | Pinned python-pptx and LibreOffice references agree with saved structure and bounded raster output. |
| round-trip | Same deck acceptance test | Notes, media, relationships, omitted gradient attributes and unmodelled text body properties survive. |
| gate | Backlog differential test gate | The full deck workflow round trips, validates and matches pinned viewer outputs. |

## HLD impact

- `docs/hld/12-testing-strategy.md`

## Risk routing

- Layout and rendering: read `docs/hld/08-rendering-spec.md`. Use deterministic bundled fonts for native output.
- External oracle: read `.claude/skills/differential-testing.md`. Assert exact python-pptx and LibreOffice versions, structural records and a justified raster tolerance.
- PyO3 test changes: read `docs/hld/10-bindings-spec.md`. Run the isolated Python suite and WASM check.
- Any parser, serialiser or public API repair discovered during the gate earns its matching risk row before editing.

## Hash harness

Expected unchanged. An observed delta blocks completion until separately declared and reviewed.

## Implementation checklist

- [x] Bind the reporter deck fixture and record its digest.
- [x] Exercise the complete saved deck chain for Issues 169, 170, 215, 216 and 217.
- [x] Assert package integrity, structural reopen, validation and pinned cross-viewer output.
- [x] Run focused differential checks, scoped verification and microscope to zero findings.

## Open questions

None. Issue 158 supplies the fixture and scripted workflow.
