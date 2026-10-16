# F-X157, correctness, pass 1

**Reviewed**: Working diff against 07b1229a, 3 files, 171 added lines
**Verdict**: 0 defects, 1 smell, 0 nitpicks

## Defects

None.

## Smells

### S1, the viewer rider can silently pass without a viewer
`crates/rpptx-py/tests/test_documented_examples.py:4341`

When neither the pinned environment variable nor `soffice` on PATH exists, the conditional skips every raster assertion and the acceptance test passes. Mark the oracle portion skipped explicitly so a scoped gate cannot mistake a structural-only pass for viewer evidence.

## Nitpicks

None.

## Not found

No correctness, contract, panic, OOXML ordering, relationship, test-gate or structure defects were found. The diff adds no source parser, serialiser, public type, trait, generic, crate or module.
