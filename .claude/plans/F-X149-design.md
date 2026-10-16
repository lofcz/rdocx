# F-X149, Word fixture and workflow acceptance gate

**Status**: completed
**Sprint**: S82
**Size**: L
**Depends on**: F-X144, F-X145, F-X146, F-X147, F-X152

## Problem

`crates/rdocx-py/tests/test_python_docx_parity.py:633` defines 17 identity attribute rows and omits the reporter's eighteenth control row with no attribute. Its producer matrix at line 652 has 11 rows, but the current cases do not establish every operation result from the reporter's script. `docs/hld/12-testing-strategy.md`, "The Word corpus", describes the matrices without a complete reporter workflow or the original attachment as a repeatable gate.

The pinned report exposes one remaining integration defect. Replacing image bytes at an existing relationship ID leaves comparison unable to represent the binary change. Its acceptance postcondition fails at body story item 2 after the reporter's first six editing steps pass.

## Spec reference

- `docs/hld/04-opc-and-packaging.md`, "Package integrity".
- `docs/hld/08-rendering-spec.md`, "The renderer's input".
- `docs/hld/10-bindings-spec.md`, "Python API shape" and "CLIs".
- `docs/hld/10-bindings-spec.md`, "Native Word facade stability".
- `docs/hld/12-testing-strategy.md`, "The Word corpus" and "Binding tests".
- `docs/hld/14-development-backlog.md`, "F-X149, Word fixture and workflow acceptance gate".

## Approach

Extend the existing Python parity entrypoint to enumerate the missing control row, exercise every identity and producer cell through its required operations, and assert observable saved structure and comparison results against pinned `python-docx==1.2.0` where that oracle can read them. Run the reporter's 12-step report workflow from DOCX through CLI and Python editing to deterministic PDF. Fetch the original attachment into ignored test storage with SHA-256 `d05f9c753c00eb804c6e345126ef7a1f7a4fc635d2c9b653b829922030cd875e`, or use a local file with that digest. The reporter's script archive has SHA-256 `e448d27c8ed4b060749b1b0cbd2c833cd2e4445d49cda9ad34069b8e80bb7f0c`. Do not check in binary fixtures.

Repair comparison of an image payload replacement in the existing comparison module. Stage the edited payload under a fresh media part and relationship, then represent the drawing change with tracked run content. Acceptance must resolve to the edited image bytes and rejection to the original bytes. Preserve unrelated package parts exactly.

Remeasure the `rdocx` package archive after the Rust diff and update its README evidence row and the matching `scripts/readme_doctests.py` carrier.

## Rejected alternatives

- Repeating the S78 matrix tests without a complete workflow leaves the S82 acceptance gap.
- Treating a source-built approximation as the attached file would misstate reporter coverage.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| differential | `test_issue_158_word_fixture_acceptance`, `test_issue_159_identity_matrix_across_operations` and `test_issue_160_producer_matrix_across_operations_and_picture` in the existing Python parity entrypoint | The attached report agrees with pinned structural references, and all 18 identity rows and 11 producer rows run their declared operations. |
| integration | `test_issue_158_complete_word_workflow` in the existing Python entrypoint | The same report survives Python save, CLI actions, reopen, deterministic render and comparison. |
| regression | Existing comparison module test for changed image bytes at one relationship ID | The redline survives save and reopen, acceptance and rejection resolve to their respective image bytes, and unrelated package parts remain unchanged. |
| repository policy | README archive measurement and `scripts.test_sprint_workflow` | The archive byte count and date agree with the package generated from this diff. |
| gate | Run the three differential tests above and `test_issue_158_complete_word_workflow` together | The report workflow and both matrices pass against pinned outputs, with attachment evidence explicitly distinguished from source-built coverage. |

## HLD impact

- `docs/hld/12-testing-strategy.md`
- `docs/hld/10-bindings-spec.md`

## Risk routing

- Layout and rendering: read `docs/hld/08-rendering-spec.md`. Use deterministic bundled fonts and verify any render baseline deliberately.
- External oracle: read `.claude/skills/differential-testing.md`. Pin and record python-docx, Word or LibreOffice versions actually used, compare parsed structure and state raster tolerance.
- PyO3 test changes: read `docs/hld/10-bindings-spec.md`. Run the isolated Python suite and the WASM check. Exclude Python binding crates from workspace tests.
- Any parser, serialiser or public API repair discovered during the gate earns its matching risk row before editing.
- Comparison package staging: read `docs/hld/04-opc-and-packaging.md` and `docs/hld/06-presentationml-model.md`. Assert package and XML round trips retain unrelated parts byte for byte and existing drawing child order. The existing Rust test and focused Python workflow are the added checks.

## Hash harness

Expected unchanged. An observed delta blocks completion until separately declared and reviewed.

## Implementation checklist

- [x] Identify the eighteenth identity row and assert the 18 by 7 and 11 by 8 cell counts.
- [x] Add mutation-sensitive assertions and pinned structural references for every operation.
- [x] Exercise one complete Python, CLI and deterministic render report chain.
- [x] Repair replacement-image comparison with accepted and rejected media and unrelated-part regression checks.
- [x] Remeasure and record the changed `rdocx` archive in both package evidence carriers.
- [x] Run the reporter's original attachment and record its digest and results.
- [x] Run focused differential checks, scoped verification and microscope to zero findings.

## Open questions

None. Issue 158 supplies the fixture and acceptance scripts. The eighteenth identity row is the no-attribute control.
