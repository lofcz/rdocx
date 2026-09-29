# Current Sprint, S75

**Milestone**: M24 Modern DOCX authoring completeness.

**Goal**: restore the hosted Python binding gate, integrate the open
contribution wave and its unassigned issue reports, then finish Issue 138's
table pagination correction before one reviewed mid-milestone merge.

## Spec references

- `docs/hld/03-architecture.md`, for native story snapshots and the integrated
  PowerPoint facade and bindings.
- `docs/hld/04-opc-and-packaging.md`, for comparison and unchanged-part
  preservation.
- `docs/hld/08-rendering-spec.md`, for Word paragraph spacing, table geometry,
  row splitting, and page flow.
- `docs/hld/12-testing-strategy.md`, for source-built regressions, the hash
  harness, and pinned deterministic rendering checks.
- `docs/hld/14-development-backlog.md`, for F-X134 through F-X136 acceptance
  contracts, dependencies, sizes, and named test gates.

## The wave

| F-ID | Title | Size | Status | Owner |
|------|-------|------|--------|-------|
| F-X134 | Keep Python story hyperlink snapshots linear | S | done | - |
| F-X135 | Integrate PRs 146 through 151 and resolve unassigned reports | L | done | - |
| F-X136 | Fix table row breaks and footer-only pages | L | done | - |

## Sequencing note

F-X134 runs first because it repairs the hosted Python binding gate inherited
from the completed story inventory work. F-X135 then integrates the six
remaining contributor branches and repairs Issues 134, 135, 136, 139, and 140,
which initially had no implementation pull request. F-X136 follows F-X135 and
closes the distinct remaining Issue 138 table-pagination report. At the
user-approved cutoff, F-X133 and F-271 through F-277 carry pending to S76.
The carry preserves their dependency order and avoids claiming unfinished
related-story work in this mid-milestone merge.

## Definition of done for this sprint

- Story hyperlink snapshots inventory namespace scopes once per physical
  source and pass the hosted Python linear-scaling gate without weakening its
  bound.
- PRs 146 through 151 are integrated at their pinned reviewed heads with
  overlaps reconciled, PR 148's missing deterministic golden baseline repaired,
  and stale per-branch archive measurements replaced by one combined record.
- Issues 134, 135, 136, 139, and 140 have source-built regressions and complete
  fixes. Issue 138 closes only after line-height and row-splitting behavior is
  implemented and verified by F-X136.
- F-271 through F-277 and F-X133 retain their pending acceptance contracts in
  S76, with the carry reason recorded at sprint close.
- The full workspace, deterministic hash harness, pinned differential oracles,
  package gates, bindings, and documentation checks pass without unexplained
  output changes.
