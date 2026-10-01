# F-X145, correctness, pass 2

**Reviewed**: working diff from `b165134f`, 11 files, 929 changed lines
**Verdict**: 2 defects, 0 smells, 0 nitpicks

## Defects

### D4, ignored comments still validate their package graph
`crates/rdocx/src/comparison.rs:534`

Both comment snapshots are built before `ignore_comments` and `ignored_stories` take effect. A damaged or missing comment-owned relationship target therefore rejects a comparison that explicitly excludes comments. The existing story filter would skip that graph.

### D5, a colliding comment asset can overwrite an unrelated story asset
`crates/rdocx/src/comparison.rs:141`

The edited comment snapshot writes each internal target at its original part name. If that name already belongs to a different asset referenced by the main story, the redline overwrites the main story bytes. Comment package restoration also leaves stale relationships for a reused target with no snapshot relationship set. The snapshot must preserve the unrelated owner and target graph.

## Smells

None.

## Nitpicks

None.

## Not found

The first pass findings on comment links, selective revision listing, and the TOC boundary test have passing focused regressions. No new schema-order or public option finding was found.
