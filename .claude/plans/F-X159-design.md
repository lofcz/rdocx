# F-X159, Word document validity baseline

**Status**: completed
**Sprint**: S83
**Size**: M
**Depends on**: F-X149

## Problem

Fresh Word-compatible packages stamp a three-component crate version into
AppVersion (`crates/rdocx/src/document.rs:5148`). Word rejects that shape.
`Cell::add_table` leaves a nested table as the last cell child
(`crates/rdocx/src/table.rs:1949`), which Word also rejects. Existing Rust
round-trip tests do not establish that Word opens the authored file.

## Spec reference

- `docs/hld/04-opc-and-packaging.md`, "The package" and "Package integrity".
- `docs/hld/12-testing-strategy.md`, "The hash harness" and "The Word corpus".
- `docs/hld/08-rendering-spec.md`, "Tables".

## Approach

Review PR 240 against the current tree. Stamp fresh AppVersion as `XX.YYYY`,
validate authored values, and repair invalid loaded values on save without
altering valid producer bytes. Append the required trailing paragraph after an
authored nested table. Keep the behavior change and its baseline update in the
F-X159 commit. Do not include unrelated README measurement changes from the
PR without confirming they follow from the compiled archive.

## Rejected alternatives

- Keep the three-component AppVersion and rely on tolerant readers. Word is the
  target reader for this gate.
- Rewrite all loaded application properties. Valid producer values must retain
  their bytes.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| differential | Source-built Word opening check from PR 240 | Fresh document and nested table open without repair in pinned Word |
| integration | Fresh package and nested table tests in the existing `rdocx` integration binary | Exact AppVersion XML, required trailing cell paragraph, save and reopen |
| regression | Invalid and valid producer AppVersion cases | Repair only invalid values, preserve valid values and unrelated XML |
| harness | `python3 scripts/hash_harness.py --check` | Only declared `feature_showcase` entries change |

The backlog test gate is differential. If the pinned Word GUI is unavailable,
record that gap explicitly and run package XML, LibreOffice and python-docx
checks without claiming Word evidence.

## HLD impact

- `docs/hld/04-opc-and-packaging.md`
- `docs/hld/12-testing-strategy.md`

## Risk routing

- Parser or serializer: read `docs/hld/04-opc-and-packaging.md` and
  `docs/hld/06-presentationml-model.md`. Check schema child order and exact
  preservation of unrelated XML through round trip.
- Layout and pagination: read `docs/hld/08-rendering-spec.md`. Record a baseline
  only in deterministic font mode and review the resulting rendering delta.
- External oracle: read `.claude/skills/differential-testing.md`. Pin the Word,
  LibreOffice and python-docx versions actually used.

## Hash harness

Expected baseline delta from PR 240: `feature_showcase:word/document.xml`,
`feature_showcase:pdf/bytes` and `feature_showcase:pdf/pages`. The new trailing
cell paragraph changes the feature showcase's Word XML and PDF. No other entry
may change without a separate explanation and review.

## Implementation checklist

- [x] Review PR 240 against the approved scope and current code.
- [x] Implement AppVersion and nested-table fixes with exact XML regressions.
- [x] Run focused tests, oracle checks, scoped verify and microscope to zero.
- [x] Record and review only the declared deterministic hash delta.

## Open questions

None. The sprint definition establishes the accepted output and baseline owner.
