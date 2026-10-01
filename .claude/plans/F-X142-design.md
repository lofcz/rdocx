# F-X142, Presentation Python contribution wave

**Status**: completed
**Sprint**: S77
**Size**: L
**Depends on**: F-X140

## Problem

The Presentation Python binding lacks several checked native deck operations
needed by Issue 169. The binding in `crates/rpptx-py/src` and facade in
`crates/rpptx/src/lib.rs` are changed by PRs 173, 181, 189, 192, 208 and
209. PRs 208 and 209 both stack on 189, and overlapping shapes, tables, stubs
and tests require one reconciled result. Those PRs leave Issue 169's shape
hyperlinks, anchored comments and built-in table styles unresolved.

## Spec reference

- `docs/hld/05-drawingml-model.md`, "Geometry", "Text body", "Tables" and
  "Preservation".
- `docs/hld/06-presentationml-model.md`, "Public facade", "The shape tree",
  "Placeholders" and "Preservation strategy".
- `docs/hld/07-inheritance-and-resolution.md`, "Position and size".
- `docs/hld/10-bindings-spec.md`, "The PyO3 lifetime problem" and "Python
  API shape".
- `docs/hld/12-testing-strategy.md`, "Binding tests" and "The deck corpus".
- `docs/hld/14-development-backlog.md`, "F-X142" and "F-X148".

## Approach

Review PRs 173, 181, 189 and 192 against the S76 prefix, then replay only
PRs 208 and 209's incremental diffs after shared PR 189 parent `6509eaf8`.
Keep Python `Run.text` handle behavior, facade basics, checked replacement,
table topology and formatting, crop, z-order, hyperlinks, effective
placeholder geometry, group population and row or column editing in one
coherent binding. Reconcile native shape and table edits and preserve unknown
XML. Add a Python `replace_text` alias with the same checked count contract.
Keep direct geometry presence distinct and expose inherited values through
`effective_geometry()`. Match python-pptx's group refit and row-height behavior
for the Issue 169 workflow. Finish the full checklist: expose shape hyperlink
read, add and retarget operations, support comment anchors on a shape or text
range, and render built-in table styles when the package omits their
definition. Keep comment anchor XML in schema order and preserve unmodelled
anchor forms verbatim. Validate the deck after save, reopen and render.

Published native additions are additive for pre-1.0 crates. Python handle
behavior and method signatures must match the stub. F-X148 becomes an
independent production fixture audit of the S77 result.

## Rejected alternatives

- Cherry-pick whole PR 208 and 209 heads. They both include PR 189.
- Expose inherited placeholder geometry as invented direct XML. Use a
  resolved accessor while keeping direct geometry presence meaningful.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| integration | Existing `rpptx-py` tests and typing smoke | Held handles, checked replacement and stub signatures agree. |
| integration | Complete Issue 169 Python and Rust deck workflow | Shapes, groups, tables, run and shape hyperlinks, and anchored comments retain structure after save and reopen. |
| round-trip | Existing `rpptx` integration entrypoint | Placeholder geometry, group transforms and table row or column operations validate and render. |
| golden | Built-in table style deck case | A built-in style ID resolves without a package definition and renders deterministically. |
| differential | Pinned python-pptx workflow cases | Public values and authored deck structure agree within declared scope. |

**Test gate**: integration, as stated in the backlog. Run focused `rpptx`,
`rpptx-py`, `rpptx-layout` and `oxml-drawing` checks, then the scoped gate.

## HLD impact

- `docs/hld/05-drawingml-model.md`
- `docs/hld/06-presentationml-model.md`
- `docs/hld/07-inheritance-and-resolution.md`
- `docs/hld/08-rendering-spec.md`
- `docs/hld/10-bindings-spec.md`
- `docs/hld/14-development-backlog.md`

## Risk routing

- Parser or serialiser: read `docs/hld/04-opc-and-packaging.md` and
  `06-presentationml-model.md`. Check schema order, prefix-tolerant parsing
  and byte-preserving round-trip of unmodelled XML.
- Layout and rendering: read `docs/hld/08-rendering-spec.md`. Use
  deterministic fonts for deck render checks and keep the baseline stable.
- Public API of published crates: read `docs/hld/10-bindings-spec.md`, state
  semver impact, run `cargo publish --dry-run` for touched crates and check
  `.crate` size.
- PyO3 bindings: read `docs/hld/10-bindings-spec.md`. Check the WASM target
  and exclude both Python crates from workspace tests.
- External oracle comparison: read `.claude/skills/differential-testing.md`,
  pin python-pptx and record its version with the comparison.

## Hash harness

Expected unchanged after F-X140 and F-X141's distinct reviewed baseline
updates.

## Implementation checklist

- [x] Review incremental PR diffs and remove inherited PR 189 changes.
- [x] Reconcile Python bindings, stubs, native facade, OXML and tests.
- [x] Run held-handle, checked mutation and Issue 169 workflow cases.
- [x] Complete shape hyperlinks, anchored comments and built-in table style
  rendering, with Python, native and saved-package parity.
- [x] Round-trip, validate and render the resulting deck.
- [x] Run focused checks, risk riders and microscope to zero findings.

## Open questions

None. S77 includes the full Issue 169 checklist and the named parity gaps.
F-X148 audits the resulting production fixture independently.
