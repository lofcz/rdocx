# F-X145, correctness, pass 1

**Reviewed**: working diff from `b165134f`, 5 files, 500 changed lines
**Verdict**: 3 defects, 0 smells, 0 nitpicks

## Defects

### D1, edited comment relationships are omitted
`crates/rdocx/src/comparison.rs:57`

The snapshot stores each comments part but neither that part's relationships nor the parts they target. An edited-side comment with a hyperlink or embedded image can enter the redline with a dangling relationship. Accepting the redline cannot reconstruct the edited comment package.

### D2, comment revision count diverges from revision listing
`crates/rdocx/src/revision.rs:336`

The private comment snapshot adds one to `accept_all` and `reject_all`, but it creates no modeled revision in `Document::story_revisions`. A comment-only redline therefore reports one resolved revision while `story_revisions` lists zero, violating the documented count and leaving a selective revision undiscoverable.

### D3, rebuilt TOC test does not establish the regression gate
`crates/rdocx-py/tests/test_python_docx_parity.py:954`

This fixture already passed before the paragraph replacement implementation. It does not reproduce Issue 161's paragraph boundary refusal, so reverting the TOC repair would leave the test green. The test must exercise a structure that fails for the intended reason before the fix.

## Smells

None.

## Nitpicks

None.

## Not found

No additional correctness, panic, or schema-order findings in the reviewed diff.
