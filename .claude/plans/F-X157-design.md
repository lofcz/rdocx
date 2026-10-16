# F-X157, Complete deck-chain authoring checklist

**Status**: completed
**Sprint**: S81
**Size**: L
**Depends on**: F-X148, F-X155, F-X156

## Problem

F-X155 proved individual shadow, effect, line end and geometry operations. F-X156 adds slide import and scoped replacement. Issue 217 remains open until all six operations are exercised together through the Python authoring chain and checked against an independent reader and viewer. The S80 drawing probe alone cannot establish that the imported and edited deck retains its package relationships and visual output.

## Spec reference

- `docs/hld/04-opc-and-packaging.md`, "Relationship types" and "Package integrity".
- `docs/hld/05-drawingml-model.md`, "Geometry", "Tables" and "Preservation".
- `docs/hld/06-presentationml-model.md`, "Public facade", "Relationship remapping" and "Validation".
- `docs/hld/07-inheritance-and-resolution.md`, "4. Style references".
- `docs/hld/08-rendering-spec.md`, "Tables" and "The renderer's input".
- `docs/hld/10-bindings-spec.md`, "Python API shape".
- `docs/hld/12-testing-strategy.md`, "The deck corpus" and "Binding tests".
- `docs/hld/14-development-backlog.md`, "F-X157, Complete deck-chain authoring checklist".

## Approach

Extend the existing source-built deck acceptance case to author all six Issue 217 items in one Python workflow: direct outer shadow, connector theme effect suppression, line ends, preset geometry, cross-deck slide import and counted replacement at slide or text-frame scope. Save, reopen in python-pptx, run `rpptx validate`, and compare LibreOffice output to deterministic rpptx rendering per item. Record exact pinned versions, comparison tolerances and package hashes. For any unsupported operation, record the reviewed boundary and a usable fallback. Repair only an integration defect found in this combined chain.

## Rejected alternatives

- Six unrelated unit tests cannot reveal interaction between effects, imported package graphs and scoped text mutation.
- A visual comparison alone cannot prove notes, links and media relationship retention.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| integration | Existing `rpptx-py` deck workflow | The six Issue 217 operations execute in one saved authoring chain. |
| round-trip | python-pptx 1.0.2 reopen and `rpptx validate` | Drawing XML, slide order, media, notes and relationships remain valid. |
| differential | LibreOffice 26.2.5.2 versus deterministic rpptx render | Each authored item meets explicit pixel tolerance or has a reviewed scope boundary and fallback. |
| gate | Backlog differential test gate | All six items have independent-reader, validator and viewer evidence or an explicit reviewed exception. |

## HLD impact

- `docs/hld/12-testing-strategy.md`
- `docs/hld/14-development-backlog.md`

## Risk routing

- Layout and rendering: read `docs/hld/08-rendering-spec.md`. Use bundled deterministic fonts.
- External oracle: read `.claude/skills/differential-testing.md`. Pin python-pptx and LibreOffice versions and record structural and pixel evidence.
- Any integration repair to a parser, serialiser, public API or PyO3 binding earns the matching additional `risk-routing` checks before editing.

## Hash harness

Expected unchanged. A newly observed output delta blocks completion until it is attributed to a declared feature behavior.

## Implementation checklist

- [x] Enumerate the six Issue 217 operations and their existing S80 evidence.
- [x] Execute the complete Python deck chain on the integrated F-X148 prefix.
- [x] Reopen in python-pptx, validate and compare pinned cross-viewer output per item.
- [x] Record reviewed scope boundaries and fallbacks for unsupported operations.
- [x] Run scoped verification and microscope to zero findings.

## Open questions

None. The S80 accepted viewer boundary uses pinned LibreOffice and python-pptx evidence and does not claim an unavailable PowerPoint 16.104 observation.
