# F-X154, all, pass 1

**Reviewed**: working diff, 12 files, 623 added lines and 42 deleted lines
**Verdict**: 1 defect, 1 smell, 0 nitpicks

## Defects

### D1, binding test expects an invalid XML character in run text
`crates/rpptx-py/tests/test_documented_examples.py:423`

The full binding suite fails when the run setter receives a vertical tab. The established run setter encodes XML 1.0 invalid characters as `_xHHHH_`, so this assertion expects a value the model cannot store. Update the test to assert the encoded value and keep the literal line feed case.

## Smells

### S1, paragraph setter repeats existing vertical tab splitting
`crates/oxml-drawing/src/text/paragraph.rs:1637`

`CT_TextParagraph::set_text` already calls `split_line_breaks`. The new `set_text_with_line_breaks` calls that setter, then contains a second splitting path that is unreachable after a vertical tab has already produced a break. Remove the redundant method and call the established setter at the new call sites.

## Nitpicks

None.

## Not found

Correctness beyond D1, preservation and OOXML child order, panics, public contract and structure produced no other findings. The foreign namespaced attribute collision found during replay was fixed before this pass and has a regression test.
