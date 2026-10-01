# F-X144, Identity and producer matrices across operations

**Status**: completed
**Sprint**: S78
**Size**: L
**Depends on**: F-X143, F-X151

## Problem

Issues 157, 159, and 160 describe operation-specific failures for ordinary Word and Google Docs XML. `crates/rdocx/src/field.rs`, `comparison.rs`, and `document.rs` each consume different views of those files, so one-case repairs have left other operations refusing or losing source identity and producer state.

## Spec reference

- `docs/hld/03-architecture.md`, "What stays put" and "Facade conventions", for content-control traversal, fields, and comparison.
- `docs/hld/04-opc-and-packaging.md`, "The package", for retained story bytes and markup compatibility.
- `docs/hld/12-testing-strategy.md`, "Test taxonomy" and "The Word corpus", for identity, producer, and package acceptance matrices.

## Approach

Reproduce the attached identity and producer matrices as source-built regression cases in existing test entrypoints. Exercise save, replacement, TOC rebuild, page field update, deterministic render, self-compare, and one-word compare for every identity row. Exercise the producer matrix, including packed footer fields, unchanged comments bytes, and `mc:Ignorable` declarations in every edited part. Add `add_picture` to the producer operation columns and cover both the default-root-namespace and inline-control rows. Repair failures at the existing owner and serializer boundaries, using the same accepted-view walker for text, replacement, exporter, image, and related-story operations.

## Rejected alternatives

- Add only the known failing rows. The backlog requires both full matrices to remain executable.
- Strip producer identity attributes before processing. Save must retain their values and source order.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| regression | Issue 159 identity matrix across seven operations | No lost identity, identity-only revision, or compare refusal |
| regression | Issue 160 producer matrix and walker locations | All producer traits and nested body, image, and related-story locations remain visible and editable |
| regression | Issue 157 `add_picture` column | Root default namespace and inline controls save and reopen with picture and original content |
| package | Comments and markup-compatibility round trips | Unchanged comments bytes and all used ignorable prefixes remain declared |
| integration | The backlog regression gate | Both matrices and the picture column pass on the integrated F-X151 prefix |

## HLD impact

- `docs/hld/03-architecture.md`
- `docs/hld/04-opc-and-packaging.md`
- `docs/hld/12-testing-strategy.md`

## Risk routing

- Parser or serialiser: read `docs/hld/04-opc-and-packaging.md` and `06-presentationml-model.md`. Check schema child order, prefix-tolerant reads, and byte preservation of unmodelled subtrees.
- Layout and pagination: read `docs/hld/08-rendering-spec.md`. Use deterministic bundled fonts for render cells.
- PyO3 bindings: read `docs/hld/10-bindings-spec.md`. Check the WASM target and use the required binding crate exclusions in workspace tests.

## Hash harness

Expected unchanged because these cases use new source-built inputs and do not intentionally change existing samples. Stop and review any observed delta.

## Implementation checklist

- [x] Reproduce every issue matrix row and operation on the F-X151 prefix.
- [x] Fix each failing owner, parser, comparator, or serializer path.
- [x] Check `mc:Ignorable` and comments bytes in all edited parts.
- [x] Run scoped verification and a zero-finding microscope.

## Open questions

None. The attached issue matrices and S78 definition of done provide the acceptance cells.
