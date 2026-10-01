# F-X145, Comparison options and redline completion

**Status**: completed
**Sprint**: S78
**Size**: L
**Depends on**: F-X143, F-X144

## Problem

`crates/rdocx/src/comparison.rs` has option-aware native comparison, but Python and CLI callers need matching controls. Issue 161 also records redline refusal or loss for edited-side comments, rebuilt TOCs, and marker positions after changed text.

## Spec reference

- `docs/hld/03-architecture.md`, "What stays put", for comparison ownership and accept/reject invariants.
- `docs/hld/10-bindings-spec.md`, "Python API shape" and "CLIs", for public option parity.
- `docs/hld/12-testing-strategy.md`, "Test taxonomy" and "Binding tests", for comparison policy and binding regression gates.

## Approach

Expose every `ComparisonOptions` field through Python keyword-only arguments and CLI flags with equivalent parsing and defaults. Preserve native `Document::compare` legacy behavior. Stage edited-side comment threads and anchors in the redline, then verify accept and reject against each side. Repair paragraph and content-control comparison for rebuilt TOC structure and marker positions, preserving unmodelled XML and relationship targets. Compare Word and Character granularity on the issue fixture and PR 205's refused case.

## Rejected alternatives

- Expose `ignore_comments` alone. That would drop edited-side review threads.
- Change the native default granularity. Existing callers rely on whole-run output.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| regression | Issue 161 option matrix in native, Python, and CLI | Same granularity, ignore fields, comments, formatting, whitespace, and story selection |
| regression | Edited-side comment add, remove, reply, resolve, and date cases | No refusal, redline carries edited threads, accept and reject reconstruct expected states |
| regression | Rebuilt TOC and PR 205 marker cases | Tracked output opens, markers follow changed text, accept equals edited and reject equals original |
| binding | Python strict typing and installed runtime checks | Keyword names and accepted values match the native option value |
| integration | The backlog regression gate | Python and CLI parity and every specified redline case pass |

## HLD impact

- `docs/hld/03-architecture.md`
- `docs/hld/10-bindings-spec.md`
- `docs/hld/12-testing-strategy.md`

## Risk routing

- Parser or serialiser: read `docs/hld/04-opc-and-packaging.md` and `06-presentationml-model.md`. Check child order and unmodelled XML preservation.
- Public API of published crates: read `docs/hld/10-bindings-spec.md`. State semver impact, run `cargo publish --dry-run`, and check `.crate` size.
- PyO3 bindings: read `docs/hld/10-bindings-spec.md`. Check the WASM target and use binding crate exclusions in workspace tests.

## Hash harness

Expected unchanged. Comparison is opt-in and existing samples do not invoke it.

## Implementation checklist

- [x] Add matching Python and CLI option surfaces and parity tests.
- [x] Carry edited-side comments into redline and verify both revision outcomes.
- [x] Repair rebuilt TOC and changed-text marker cases.
- [x] Run scoped verification and a zero-finding microscope.

## Open questions

None. `docs/hld/10-bindings-spec.md` explicitly keeps `run` as the CLI and Python default, with `word` and `character` available by choice.
