# F-X151, Word preservation and comparison PR intake

**Status**: completed
**Sprint**: S78
**Size**: L
**Depends on**: F-X143

## Problem

Six Word contributions remain on PR branches based before the S77 prefix. Directly applying each complete branch would undo S77 facade and comparison work. The intake must retain only the incremental commits and reconcile shared files, especially `crates/rdocx/src/document.rs`, `comparison.rs`, and `regression_test.rs`.

## Spec reference

- `docs/hld/03-architecture.md`, "What stays put" and "Facade conventions", for story, comparison, exporter, and preservation ownership.
- `docs/hld/04-opc-and-packaging.md`, "The package", for exact source bytes, relationships, and package validation.
- `docs/hld/05-drawingml-model.md`, "Text", for DrawingML XML-character handling.
- `docs/hld/08-rendering-spec.md`, "The PDF backend", for caller font fallback and layout output.
- `docs/hld/10-bindings-spec.md`, "Python API shape" and "CI", for the failed PR 239 binding gate.
- `docs/hld/12-testing-strategy.md`, "Test taxonomy", "The hash harness", and "Binding tests", for regression and output evidence.

## Approach

Review the incremental source commits from PRs 214, 228, 229, 232, 233, and 239 against their own bases. PR 233 also repairs DrawingML and Presentation XML-character handling, which remains part of its incremental acceptance and requires the DrawingML HLD update. Port their behavior and tests onto the S77 prefix in separate labelled commits. Reconcile PR 214 caller font fallback with PR 194's current implementation. Preserve the existing S77 public APIs and avoid carrying stale archive measurements or unrelated branch rewrites. Run the PR 239 Python binding gate on the reconciled result.

## Rejected alternatives

- Merge whole PR heads. Their older base would reverse completed S77 work.
- Re-record archive or hash baselines as a conflict remedy. Measurements must follow the integrated source and every behavioral delta needs a reviewed cause.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| regression | Port each PR's focused cases into existing test entrypoints | Caller font fallback, comparison accept and reject, exporter traversal, namespace and border retention, XML lexical refusal, shared-run fields |
| binding | Re-run the rdocx Python binding suite, including the PR 239 failing path | Installed binding behavior agrees with the native field model |
| package | Save and reopen edited producer and comparison fixtures | Opaque parts and unmodelled XML remain intact |
| integration | The backlog regression gate | Producer, compare, and package round trips pass on the combined prefix |
| harness | `python3 scripts/hash_harness.py --check` | Unchanged outputs or a separately reviewed and labelled delta |

## HLD impact

- `docs/hld/03-architecture.md`
- `docs/hld/04-opc-and-packaging.md`
- `docs/hld/05-drawingml-model.md`
- `docs/hld/08-rendering-spec.md`
- `docs/hld/10-bindings-spec.md`
- `docs/hld/12-testing-strategy.md`

## Risk routing

- Parser or serialiser: read `docs/hld/04-opc-and-packaging.md` and `06-presentationml-model.md`. Check schema child order, prefix-tolerant reads, and byte preservation of unmodelled subtrees.
- Layout and pagination: read `docs/hld/08-rendering-spec.md`. Use deterministic bundled fonts for output checks.
- Public API of published crates: read `docs/hld/10-bindings-spec.md`. State semver impact, run `cargo publish --dry-run`, and check `.crate` size.
- PyO3 bindings: read `docs/hld/10-bindings-spec.md`. Check the WASM target and use the required binding crate exclusions in workspace tests.

## Hash harness

Expected unchanged unless an individual imported behavior changes a sampled output. Any such change gets its own labelled commit, expected delta, and review before recording a new baseline.

## Implementation checklist

- [x] Review the incremental diff and provenance of each PR.
- [x] Port behavior and focused tests in dependency order, reconciling overlaps against S77.
- [x] Re-run PR 239's failed Python binding gate.
- [x] Run scoped verification and a zero-finding microscope.

## Open questions

None. Preserve the completed S77 API and apply only each PR's incremental behavior.
