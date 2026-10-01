# F-X151, correctness, pass 1

**Reviewed**: staged worker diff against `a684d673`, 49 files, 5,447 insertions and 893 deletions.
**Verdict**: 2 defects, 0 smells, 0 nitpicks.

## Defects

### D1, authored UTF-16 XML can publish a forbidden character

`crates/oxml-opc/src/package.rs:593`

`first_invalid_xml_character` returns before checking any UTF-16 entry. The public package writer can receive one through `set_part`, and its new test writes a newly authored UTF-16 XML part containing U+0001 successfully. That contradicts the validation contract enforced for UTF-8 and permits a malformed XML part in a newly written package. Preserve an unchanged producer part if required, but reject newly authored or changed UTF-16 XML with a forbidden character.

### D2, a quoted relationship id in unrelated XML can retain a dropped hyperlink

`crates/rdocx/src/comparison.rs:397`

The carry check searches every byte of the candidate story for a quoted id. A literal text or unrelated attribute with that value counts as a surviving hyperlink even when no `r:id` names it. The redline then gains an unrelated hyperlink relationship that neither selected story content nor the original relationship graph requires. Check an actual relationship attribute in the owning XML.

## Smells

None.

## Nitpicks

None.

## Not found

The review found no additional contract, panic, OOXML child-order, test-gate, or structural findings in the imported diff. The source-built regression gate, binding rerun, and package and hash checks provide the observed coverage.
