# F-X148, Complete rpptx Python production checklist

**Status**: completed
**Sprint**: S81
**Size**: L
**Depends on**: F-X142, F-X156

## Problem

F-X142 delivered the Issue 169 Python API foundation in S77, but the production checklist was deferred until cross-deck slide import and scoped replacement landed. The existing tests prove individual operations, while `crates/rpptx-py/tests/test_documented_examples.py` does not yet prove the entire saved deck chain from Python against independent readers and rendering. A passing individual PR is not evidence for the integrated workflow.

## Spec reference

- `docs/hld/05-drawingml-model.md`, "Geometry", "Tables" and "Preservation".
- `docs/hld/06-presentationml-model.md`, "Public facade", "The shape tree" and "Validation".
- `docs/hld/07-inheritance-and-resolution.md`, "4. Style references".
- `docs/hld/08-rendering-spec.md`, "Tables" and "The renderer's input".
- `docs/hld/10-bindings-spec.md`, "Python API shape".
- `docs/hld/12-testing-strategy.md`, "The deck corpus" and "Binding tests".
- `docs/hld/14-development-backlog.md`, "F-X148, Complete rpptx Python production checklist".

## Approach

Build one source-created production deck exercise in the existing Python test entrypoint. Cover slide and shape handles, layout, placeholders, groups, table mutation and built-in styles, crop, z-order, hyperlink, anchored comment, geometry, import and scoped replacement. Save and reopen through rpptx and python-pptx, validate package relationships and render with deterministic fonts against pinned LibreOffice. Keep a per-item pass or reviewed scope decision with an actionable fallback. If the integrated run reveals a defect, repair only an F-X148-scoped integration gap, with an independent review pass.

## Rejected alternatives

- Reusing the S77 feature test result would not cover the S81 import and scoped replacement prefix.
- Claiming unsupported behavior from isolated API tests would not prove the production deck chain.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| integration | Existing `rpptx-py` documented examples and typing smoke | Every Issue 169 checklist operation executes from Python on a source-built deck with stable handles and typed methods. |
| round-trip | Saved deck reopen through rpptx and python-pptx 1.0.2 | Slide order, shapes, tables, links, comments, media and notes retain intended values. |
| differential | LibreOffice 26.2.5.2 versus deterministic rpptx render | Pinned deck output meets explicit pixel and structural tolerances. |
| gate | Backlog integration test gate | Complete deck chain round-trips, validates and renders, and every checklist item passes or has an explicit reviewed scope boundary. |

## HLD impact

- `docs/hld/10-bindings-spec.md`
- `docs/hld/12-testing-strategy.md`

## Risk routing

- Layout and rendering: read `docs/hld/08-rendering-spec.md`. Use deterministic bundled fonts for output comparison.
- External oracle: read `.claude/skills/differential-testing.md`. Pin python-pptx and LibreOffice versions with explicit tolerances.
- Any integration repair to a parser, serialiser, public API or PyO3 binding earns the matching additional `risk-routing` checks before editing.

## Hash harness

Expected unchanged. Investigate any delta against the approved F-X156 behavior before accepting it.

## Implementation checklist

- [x] Enumerate every Issue 169 operation and its evidence or explicit scope boundary.
- [x] Run the integrated Python production deck chain, package validation and reopen checks.
- [x] Compare deterministic rpptx output with the pinned external viewer.
- [x] Repair an in-scope integration gap if found and rerun its affected checks. No product integration gap was found.
- [x] Run scoped verification and microscope to zero findings.

## Open questions

None. The S81 definition of done permits an explicit reviewed scope boundary and fallback where an operation remains unsupported.
