# F-X134, Keep Python story hyperlink snapshots linear

**Status**: completed
**Sprint**: S75
**Size**: S
**Depends on**: F-X116

## Problem

`Document::story_link_snapshots` batches owners, items, and item namespace
scopes, but `story_link_info` at `crates/rdocx/src/document.rs:13139` calls
`story_item_text` with the complete story XML. That scanner at
`crates/rdocx/src/document.rs:7808` advances from byte zero to the hyperlink
start for every link. The Python scaling gate at
`crates/rdocx-py/tests/test_core.py:283` therefore observes superlinear work on
hyperlink-rich documents.

## Spec reference

- `docs/hld/03-architecture.md`, "Container-neutral Word story editing".
- `docs/hld/10-bindings-spec.md`, "Python API shape", the owned story snapshot
  contract.
- `docs/hld/12-testing-strategy.md`, "Test categories", the binding complexity
  and installed Python gates.
- `docs/hld/14-development-backlog.md`, "F-X134, Keep Python story hyperlink
  snapshots linear".

## Approach

Collect hyperlink spans before building `LinkInfo` records. Inventory the exact
namespace scope at every hyperlink start with one
`story_namespace_scopes_at` pass per physical source. Require
`story_link_info` to receive that exact scope and extract text through
`story_item_text_with_scope`, which scans only the bounded hyperlink fragment.
Apply the same batching to both one-story and all-story hyperlink snapshots so
neither public route retains the quadratic prefix scan.

Keep relationship resolution, source ordering, nested-owner isolation, and
deduplication unchanged. Add no public type or method.

## Rejected alternatives

- Relax the Python timing bound. Two hosted failures reproduce real quadratic
  prefix work, so a wider threshold would hide the defect.
- Cache Python snapshot records on `Document`. Mutation invalidation would be
  broader than fixing the bounded native scan.
- Parse each hyperlink scope independently with `story_namespace_scope_at`.
  That repeats the same prefix scan and preserves the complexity bug.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| regression | `story_link_snapshots_do_not_rescan_story_prefix_per_link` | Hyperlink snapshots perform no repeated full-story prefix scan and retain exact ordered text and anchors. |
| integration | `test_python_story_inventory_scales_linearly` | The installed Python binding keeps correct item and link counts and the existing bounded doubling ratio. |
| regression | existing story namespace and hyperlink tests | Alias scopes, nested ownership, relationship resolution, source order, and deduplication remain unchanged. |

The **test gate** is the regression test named in the backlog. The installed
Python scaling test remains a required supporting gate and must pass unchanged.

## HLD impact

- `docs/hld/03-architecture.md`
- `docs/hld/10-bindings-spec.md`
- `docs/hld/12-testing-strategy.md`
- `docs/hld/14-development-backlog.md`

## Risk routing

- WASM or PyO3 bindings. Read `docs/hld/10-bindings-spec.md`, run the installed
  Python suite, run the WASM target check, and retain the required binding-crate
  exclusions on workspace tests.

## Hash harness

Expected to be unchanged because hyperlink snapshot inspection does not alter
document serialization, layout, or sample generation.

## Implementation checklist

- [x] Add deterministic prefix-work instrumentation and the failing regression.
- [x] Batch exact hyperlink namespace scopes in both native snapshot routes.
- [x] Extract link text from bounded namespace-complete fragments.
- [x] Run the focused Rust, installed Python, WASM, hash, microscope, and full
  verification gates.

## Open questions

None.
