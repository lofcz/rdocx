# F-X155, all aspects, pass 1

**Reviewed**: Working diff against `9ac645aa299ea9c3fe4e6f9d6e76728f8dbdb74c`, 31 files, 4,019 added and 344 deleted lines
**Verdict**: 1 defect, 0 smells, 1 nitpick

## Defects

### D1, released hyperlink can lose its local relationship namespace

`crates/rpptx-oxml/src/relmap.rs:260`

When the hyperlink declares its own `xmlns:r`, `release_hyperlinks` replaces the entire element with a new element containing `r:id` but drops that declaration. Deleting the target slide then leaves an unbound `r` prefix if no ancestor declares it. Keep a local namespace declaration in the replacement or use a prefix already declared on an ancestor. Add a case with a locally declared relationship namespace.

## Smells

None.

## Nitpicks

- `crates/rpptx/src/lib.rs:7773`, the documentation repeats the first line of the next comment.

## Not found

No other correctness, contract, panic, OOXML order, preservation, test coverage, or structure findings in this pass. The authored deck was checked by deterministic rendering, pinned LibreOffice 26.2.5.2 and python-pptx 1.0.2. PowerPoint 16.104 remains unverified under the accepted S80 scope decision.
