# F-X151, correctness, pass 2

**Reviewed**: worker diff against `a684d673` after pass 1 remediation.
**Verdict**: 0 defects, 0 smells, 0 nitpicks.

## Defects

None.

## Smells

None.

## Nitpicks

None.

## Not found

The package writer now checks forbidden XML 1.0 characters in UTF-16 entries with and without a byte order mark. The new test rejects authored UTF-16 control and noncharacter values while retaining valid UTF-16 and opaque binary parts. The comparison carry check reads relationship-namespace attributes from the owning story. Its regression first failed with a bookmark name equal to the carried id and now passes, while the inserted-hyperlink retention test also passes. Changed-crate tests, the Python suite, the hash harness, and scoped repository, font, WASM, documentation, and packaging gates passed on this reviewed diff. No additional preservation, schema-order, or diagnostic finding was found.
