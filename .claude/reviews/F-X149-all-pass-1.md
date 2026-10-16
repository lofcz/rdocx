# F-X149, all, pass 1

**Reviewed**: working diff, 7 files, 428 changed lines
**Verdict**: 1 defect, 0 smells, 1 nitpick

## Defects

### D1, named differential gate does not execute the matrices

`crates/rdocx-py/tests/test_python_docx_parity.py:998`

The plan names `test_issue_158_word_fixture_acceptance` as the differential gate for all 18 identity and 11 producer rows. That test checks only the row counts and attached report structure. It passes if an operation assertion is removed from either parametrized matrix test, so the named gate does not prove its declared operations. Name the combined test selection as the gate or make this test execute the matrix cases.

## Smells

None.

## Nitpicks

- `crates/rdocx-py/tests/test_python_docx_parity.py:7`, `sys` is unused.

## Not found

No other correctness, contract, panic, OOXML, test, or structure findings.
