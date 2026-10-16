# F-X150, Reconcile issue closure evidence

**Status**: completed
**Sprint**: S82
**Size**: M
**Depends on**: F-X149, F-X158

## Problem

`docs/sprints/SPRINT_PLAN.md:1885` lists closure criteria from the original 22-issue snapshot. Prior sprint records contain focused evidence and some closed issues, but no single criterion-level reconciliation against the integrated S76 to S82 result. Issue 158 requires both attachment gates before closure.

## Spec reference

- `docs/hld/12-testing-strategy.md`, "The Word corpus", "The deck corpus" and "Binding tests".
- `docs/hld/14-development-backlog.md`, "F-X150, Reconcile issue closure evidence".
- `docs/sprints/SPRINT_PLAN.md`, "S82, Production acceptance and issue closure evidence" and the original issue closure table.

## Approach

Build a criterion ledger in `docs/hld/12-testing-strategy.md`, with one row per issue in the 22-issue snapshot. Each row names a passing test or observation on the integrated prefix, or says unresolved and identifies the gap. Check live issue state. Record candidate closures and open decisions for `/close-sprint`, which alone reconciles external issues after integrated `main` is pushed. Issue 158 remains open while any child criterion is unresolved. The final `/verify --full` and `/sprint-review` must pass before any closure action.

## Rejected alternatives

- A PR merge or previous issue closure alone does not prove the original acceptance criteria.
- A summary count without per-criterion links hides unsupported claims.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| integration | Criterion ledger audit | Every criterion from the 22-issue snapshot has a passing artifact or explicit unresolved result. |
| regression | `test_s82_original_issue_closure_ledger_has_resolvable_evidence` in existing sprint workflow tests | Every passing row resolves to a real test, every original issue appears once, and open decisions follow unresolved rows. |
| gate | Criterion ledger audit | Ledger is complete on the integrated prefix. Final sprint verify and review pass before `/close-sprint` acts on issues. |

## HLD impact

- `docs/hld/12-testing-strategy.md`

## Risk routing

none. This story records evidence and issue state without changing product behavior. Any implementation needed to make a criterion pass requires a separately reviewed feature scope.

## Hash harness

Expected unchanged.

## Implementation checklist

- [x] Enumerate every criterion in the original 22-issue snapshot.
- [x] Bind each to a current passing artifact or an explicit unresolved result.
- [x] Confirm both fixture gates and record that final integrated verification and review remain required before any issue closure.
- [x] Reconcile live issue state, prepare post-main closure candidates for `/close-sprint`, and identify incomplete issues to leave open.
- [x] Run scoped verification and microscope to zero findings.

## Open questions

None. GitHub issue access is available through the approved remote read path. Both reporter attachments are identified and SHA-bound. An incomplete criterion keeps its issue open. `/close-sprint` performs external issue reconciliation only after the integrated `main` push.
