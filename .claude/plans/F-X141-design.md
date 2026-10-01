# F-X141, Word Python contribution wave

**Status**: completed
**Sprint**: S77
**Size**: L
**Depends on**: F-X139, F-X140

## Problem

The native Word facade has table, story, replacement, comparison and rendering
operations that the Python surface does not yet expose together. For example,
the Python document binding in `crates/rdocx-py/src/document.rs` lacks the
contributed Issue 168 workflow. PRs 176, 187, 194, 201, 203 and 212 fill
parts of that gap, but overlap on the binding, stub, native document facade
and tests. PR 194 includes S76 PR 179's commits, and PR 203 stacks on 201.
The contributed section update rejects missing partners, native new documents
lack the common styles added by Python, and native TOC insertion makes static
entries that `rebuild_toc` cannot refresh.

## Spec reference

- `docs/hld/03-architecture.md`, "Facade conventions" and "What stays put".
- `docs/hld/04-opc-and-packaging.md`, "The package".
- `docs/hld/08-rendering-spec.md`, "Word bookmark field pagination".
- `docs/hld/10-bindings-spec.md`, "The PyO3 lifetime problem", "Python API
  shape" and "Native Word facade stability".
- `docs/hld/12-testing-strategy.md`, "Binding tests".

## Approach

Review and replay only the incremental changes of the six PRs on the completed
F-X139 and F-X140 prefix. Exclude PR 194's inherited PR 179 commits. Apply
PR 203 after 201, using its incremental diff from `ca1f1be9`. Reconcile the
shared `rdocx-py` document binding, stub, tests and HLD 10 so each contributed
API remains present. Expose comparison options, tables and sections, bookmarks
and fields, section stories, counted replacement, rendering, writable paragraph
text, core properties, common styles and numbering, hyperlink retargeting and
picture resize through checked native operations. Keep handle invalidation
and error behavior consistent with the existing Python binding.

Published Rust API additions are additive except PR 201's new public
`CoreProperties` fields, which can break external struct literals before 1.0.
Binding and stub additions must agree exactly. Extend `update_section` to use
the same layout defaults as native section setters when a partner is absent,
without rewriting unrelated explicit values. Initialize native new documents
with the common styles, preserving existing producer styles. Make new TOCs
refreshable by `rebuild_toc` and return an error if insertion cannot allocate
valid heading bookmarks. Preserve the existing read path for producer TOCs.
Run the named Issue 168 acceptance cases for these changes. Run removal,
writable XML or package access and text-anchored comments remain assigned to
F-X147.

## Rejected alternatives

- Merge entire stacked PR heads. That would replay S76 and PR 201 work.
- Duplicate native logic in PyO3. The binding must use the checked facade.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| integration | Issue 168 contributed Python workflow | Tables, sections, styles, fields and replacement retain values after save and reopen. |
| integration | Existing `rdocx-py` tests, typing smoke and stubtest | Runtime, type stub and native facade agree, including handle invalidation. |
| integration | Existing `rdocx` tests | Table, story, field, style and picture operations preserve native behavior. |
| integration | Native and Python section, style and TOC parity cases | Missing section partners use layout defaults, new native documents define common styles, and TOC entries refresh after headings change. |
| regression | Deterministic rendering and package checks | PDF and section layout work after Python edits with no unexpected output delta. |

**Test gate**: integration, as stated in the backlog. Run focused `rdocx`,
`rdocx-py` and affected crate checks, then the scoped gate.

## HLD impact

- `docs/hld/03-architecture.md`
- `docs/hld/04-opc-and-packaging.md`
- `docs/hld/08-rendering-spec.md`
- `docs/hld/10-bindings-spec.md`
- `docs/hld/14-development-backlog.md`

## Risk routing

- Parser or serialiser: read `docs/hld/04-opc-and-packaging.md` and
  `06-presentationml-model.md`. Check child order and byte-preserving
  round-trip of unmodelled XML.
- Layout and rendering: read `docs/hld/08-rendering-spec.md`. Use deterministic
  fonts for the rendered acceptance cases and check the hash harness.
- Crate dependency graph: read `docs/hld/03-architecture.md` and verify the
  binding dependency graph keeps the family direction.
- Public API of published crates: read `docs/hld/10-bindings-spec.md`, record
  the `CoreProperties` semver impact, run `cargo publish --dry-run` for touched
  crates and check `.crate` size.
- PyO3 bindings: read `docs/hld/10-bindings-spec.md`. Check the WASM target
  and run workspace tests with both Python binding crates excluded.
- External oracle comparison, if used: read
  `.claude/skills/differential-testing.md` and pin the python-docx version.

## Hash harness

F-X141 owns a second, separately labelled baseline update after F-X140.
The measured delta is exactly 16 keys: `word/styles.xml` for all seven
samples, plus `word/document.xml`, `pdf/pages` and `pdf/bytes` for each of
`feature_showcase`, `proposal` and `report`. The common style definitions
change every fresh Word styles part. The three samples that insert a TOC gain
dynamic field markers and an updated PDF page-content stream. All PNG and PDF
resource keys remain unchanged. Record these keys in a separate labelled
baseline commit. PR 201 also changes a bundled presentation template SHA
expectation, which must be reviewed separately.

## Implementation checklist

- [x] Review incremental PR diffs and exclude inherited commits.
- [x] Reconcile binding, stub, native and test overlap without losing APIs.
- [x] Run the contributed Issue 168 workflow after save and reopen.
- [x] Complete section defaults, native common styles and refreshable TOC
  behavior with parity tests and a distinct reviewed baseline update.
- [x] Run typing, native parity, rendering and risk checks.
- [x] Run microscope to zero defects and smells.

## Open questions

None. S77 includes the three named behavior expansions and a separate
reviewed F-X141 baseline update. Run removal, writable XML or package access
and text-anchored comments remain for F-X147. Issue 168 stays open.
