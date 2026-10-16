# F-X152, Full-story CLI diff and count repair

**Status**: completed
**Sprint**: S79
**Size**: M
**Depends on**: F-X143, F-X151

## Problem

`crates/rdocx-cli/src/commands.rs:745` compares only `Document::paragraphs()`. It misses changed cells and related stories. Its `changes` calculation at line 803 counts one replaced paragraph as both a removal and an addition.

## Spec reference

- `docs/hld/03-architecture.md`, the full-story comparison model and native story ownership.
- `docs/hld/10-bindings-spec.md`, "CLIs".
- `docs/hld/12-testing-strategy.md`, "Binding tests" and the CLI integration coverage.
- `docs/hld/14-development-backlog.md`, "F-X152, Full-story CLI diff and count repair".

## Approach

Review PR 236's incremental diff and replay its story-aware comparison on the integrated S78 prefix. Use existing body and related-story walkers so each paragraph has one location. Pair comparable stories deterministically, report unreadable stories, and match paragraph sequences within each story with bounded Myers work. Count a replacement once, preserving separate added and removed counts. Reconcile the proposed `--json` and `--exit-code` CLI options with the current CLI envelope and error behavior. Avoid a second story model.

## Rejected alternatives

- Comparing only flattened body text would lose story locations and repeat the Issue 227 defect.
- Keeping the quadratic whole-document LCS would make large story comparisons unnecessarily expensive.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| regression | Issue 227 reproducer | Changed body cells and related-story paragraphs are located and each changed paragraph counts once. |
| integration | Existing `crates/rdocx-cli/tests/integration.rs` diff cases | Nested cells, headers, notes, comments and text boxes report stable one-based locations and no duplicate control text. |
| integration | CLI exit and JSON cases | Identical, changed, unreadable and closed-pipe cases have the documented status and schema. |
| regression | Large paragraph sequence | First and last edits in at least 5001 paragraphs complete within the test budget. |
| gate | Backlog regression test gate | The issue reproducer locates every changed story paragraph and counts each changed paragraph once. |

## HLD impact

- `docs/hld/10-bindings-spec.md`

## Risk routing

- Public API of published `rdocx` if the story snapshot accessor is needed: read `docs/hld/10-bindings-spec.md` and `CLAUDE.md` structural rules, state its additive semver impact, run `cargo publish --dry-run` and assert package size.

## Hash harness

Expected unchanged. The CLI diff writes no DOCX or rendering output.

## Implementation checklist

- [x] Review PR 236's incremental diff and reconcile overlaps with the S78 CLI and story walkers.
- [x] Add failing Issue 227 and focused story, count, exit and JSON cases to existing test entrypoints.
- [x] Implement story-aware diff and bounded matching with deterministic locations.
- [x] Run CLI and facade tests, scoped verification and a zero-finding microscope.

## Open questions

None. PR 236 describes additive machine-readable output, and the sprint contract requires its story and count behavior.
