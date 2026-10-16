# F-X146, all, pass 1

**Reviewed**: `git diff 1dc1f457` including working changes, 40 files, 3,740 added lines and 672 deleted lines
**Verdict**: 1 defect, 0 smells, 0 nitpicks

## Defects

### D1, a long TOC number can overflow while measuring its direct tab stop

`crates/rdocx/src/field.rs:6593`

`toc_number_tab_stop` sums one advance per marker character into `u32`, then multiplies the rounded stop as `i32`. A producer-controlled numbering marker long enough to exceed either range panics in debug builds or wraps in release builds. Rebuild of a TOC can therefore fail instead of returning bounded geometry. Use saturating arithmetic through the final twip conversion and test a marker longer than the numeric range.

## Smells

None.

## Nitpicks

None.

## Not found

The contract, OOXML ordering and retained-XML, tests, structure, and other panic paths reviewed produced no additional findings. The rich-line and tab wrap conflict resolution preserves hanging-space fitting, and the focused cases passed. The public `TabAlignedField` and added struct fields are declared in the revised plan with their source compatibility impact.
