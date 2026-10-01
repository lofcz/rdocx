# F-X143, default, pass 1

**Reviewed**: F-X143 diff against `b6c1db89`, 19 files, 1,910 additions and 72 deletions.
**Verdict**: 1 defect, 0 smells, 0 nitpicks.

## Defects

### D1, validation accepts CDATA outside the XML root

`crates/rdocx-cli/src/commands.rs:1829`

`xml_style_references` ignores `Event::CData` when the element stack is empty.
A generated DOCX whose header starts with `<![CDATA[outside]]>` was reported
with warnings only and exit status 0 by `rdocx validate`. That related part is
not well-formed XML. Reject non-whitespace character data outside the root and
cover it with a CLI regression.

## Smells

None found.

## Nitpicks

None found.

## Not found

No further findings in contract, panics, OOXML preservation and order, tests,
or structure.
