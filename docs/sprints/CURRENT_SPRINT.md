# Current Sprint, S78

**Milestone**: X, contribution intake and issue repair.

**Goal**: land the reviewed Word preservation and comparison contributions on
the completed S77 prefix. Then run the attached identity and producer matrices
and finish the comparison and redline acceptance criteria for Issues 157, 159,
160 and 161.

## Spec references

- `docs/hld/03-architecture.md`, for the Word facade, comparison and CLI crate
  boundaries.
- `docs/hld/04-opc-and-packaging.md`, for source-preserving story and package
  round trips.
- `docs/hld/08-rendering-spec.md`, for layout-backed field and TOC behavior
  exercised by the producer matrix.
- `docs/hld/10-bindings-spec.md`, for Python and CLI comparison options and
  redline parity.
- `docs/hld/12-testing-strategy.md`, for deterministic fixtures, the hash
  harness and the integrated regression gate.
- `docs/hld/14-development-backlog.md`, for the F-X151, F-X144 and F-X145
  contracts and their dependencies.

## The wave

| F-ID | Title | Size | Status | Owner |
|------|-------|------|--------|-------|
| F-X151 | Word preservation and comparison PR intake | L | done | - |
| F-X144 | Identity and producer matrices across operations | L | done | - |
| F-X145 | Comparison options and redline completion | L | done | - |

## Sequencing note

F-X151 follows completed F-X143 and reviews the incremental changes in PRs
214, 228, 229, 232, 233 and 239 against the S77 prefix. Resolve PR 214's
recorded overlap with PR 194, and rerun the failed PR 239 Python binding gate
on the reconciled result. F-X144 follows F-X151 and completes the identity,
producer and add_picture matrices. F-X145 follows F-X144 and completes the
comparison option, edited-side comment, rebuilt TOC and marker-order cases.
Use focused checks and a zero-finding microscope per story, then the full
verification and sprint review once on the combined result.

## Definition of done for this sprint

- Each contributed PR has a reviewed incremental diff on the S77 prefix, with
  overlaps reconciled and focused checks passing on the replayed result.
- The Issue 157 add_picture column and all Issue 159 identity rows pass every
  named operation without lost identity or comparison refusal.
- The Issue 160 producer matrix, content-control walker locations, unchanged
  comments bytes and mc:Ignorable checks pass on edited parts.
- Python and CLI comparison options agree. Edited-side comments, rebuilt TOCs
  and marker placement meet every Issue 161 redline case.
- The combined result passes the hash harness, the applicable binding and
  package regressions, `/verify --full` and `/sprint-review` before closure.
- At `/close-sprint`, reconcile S78 PRs and issues against verified `main`,
  thank contributors in specific human-written comments, and close issues
  only after their complete criteria have evidence.
