# F-X153, Word Python supplemental contribution

**Status**: completed
**Sprint**: S79
**Size**: M
**Depends on**: F-X141, F-X144

## Problem

The CLI comment command lacks the text-anchor route supplied by PR 220. Python run handles in `crates/rdocx-py/src/run.rs` lack a structural remove operation. Existing numbering validation can reject the allowed level-only `Subtitle` style in python-docx's template. These gaps prevent the sprint's save-and-reopen checklist cases.

## Spec reference

- `docs/hld/03-architecture.md`, native comment and run ownership.
- `docs/hld/04-opc-and-packaging.md`, preservation of unmodelled Word XML.
- `docs/hld/10-bindings-spec.md`, "Python API shape", "Native Word facade stability" and "CLIs".
- `docs/hld/12-testing-strategy.md`, "Binding tests".
- `docs/hld/14-development-backlog.md`, "F-X153, Word Python supplemental contribution".

## Approach

Review PR 220's incremental diff and reconcile it with F-X141's integrated Python and CLI APIs. Route CLI text anchors through the existing native comment operation with explicit occurrence validation. Resolve level-only numbering through the based-on style chain while preserving the original style XML. Add a native `Paragraph::remove_run(run_index)` and Python `Run.remove()` using existing accepted-view run paths and revision invalidation. Preserve comment, bookmark and permission markers and refuse removal that would orphan references or split a complex field. Save and reopen each accepted mutation before treating it as complete.

## Rejected alternatives

- Editing run XML directly in Python would bypass native ownership, markers and revision rules.
- Rejecting every level-only numbering style would continue to reject a valid python-docx template.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| integration | CLI anchored comment cases | Requested occurrence gets exact coordinates, bad ranges fail without output, and metadata survives reopen. |
| regression | Level-only numbering cases | `Subtitle`, inherited levels and direct paragraph levels validate and render after reopen. |
| integration | Native and Python run removal cases | Body and cell runs disappear, markers and retained controls survive, unsafe cases refuse atomically, and stale handles are detected. |
| integration | Issue 168 remaining-work record | Completed comment, numbering and run cases are distinguished from work assigned to F-X147. |
| gate | Backlog integration test gate | Python and CLI comment coordinates, numbering and run removal pass after save and reopen. |

## HLD impact

- `docs/hld/04-opc-and-packaging.md`
- `docs/hld/10-bindings-spec.md`

## Risk routing

- Parser or serialiser changes in Word text and wrapper ownership: read `docs/hld/04-opc-and-packaging.md` and `06-presentationml-model.md`. Check schema order and round-trip preservation of unmodelled XML byte for byte.
- Public native API: read `docs/hld/10-bindings-spec.md` and `CLAUDE.md` structural rules, state additive semver impact, run `cargo publish --dry-run` and assert package size.
- PyO3 binding: read `docs/hld/10-bindings-spec.md`, run the binding suite and `cargo check --target wasm32-unknown-unknown -p rdocx-wasm -p rpptx-wasm`. Exclude the Python binding crates from workspace tests.

## Hash harness

Expected unchanged. These operations affect authored documents and validation, not the harness inputs.

## Implementation checklist

- [x] Review PR 220's incremental diff and reconcile F-X141 overlap.
- [x] Add failing CLI, native and Python cases to existing test entrypoints.
- [x] Implement text anchors, numbering validation and run removal with atomic refusal.
- [x] Run focused CLI, facade, Python and XML preservation checks.
- [x] Run scoped verification and a zero-finding microscope.

## Open questions

None. F-X147 owns the remaining Issue 168 checklist after this contribution.
