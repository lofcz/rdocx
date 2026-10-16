# F-X159, correctness, pass 1

**Reviewed**: working diff against claim base `7116f2b2`, 6 files, 216 changed lines
**Verdict**: 1 defect, 1 smell, 0 nitpicks

## Defects

### D1, stale hash rationale now describes the wrong change

`docs/hld/12-testing-strategy.md:1367`

The paragraph after the new F-X159 delta still says the delta exists because extraction changed unit conversion and text shaping. The recorded delta comes from the trailing cell paragraph. The statement misattributes the current baseline change and breaks the design plan's reviewed-delta record.

## Smells

### S1, repair coverage does not prove unknown application XML survives

`crates/rdocx/tests/integration_test.rs:1757`

The invalid-version repair cases contain only modeled children. The plan requires unrelated XML to survive repair, but this test would pass if an unmodeled extension subtree were dropped when application properties are serialized. Add one exact preserved extension case.

## Nitpicks

None.

## Not found

Correctness, contract, panics, OOXML child order and structure produced no other finding. The nested-table regression failed against the unmodified source and Word 16.113.2 opened the new showcase without repair.
