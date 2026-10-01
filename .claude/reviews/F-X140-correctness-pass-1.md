# F-X140, correctness, pass 1

**Reviewed**: Worker diff from claim base through the isolated ToUnicode commit and the current working tree, 34 files, 2,365 added lines and 141 removed lines.
**Verdict**: 0 defects, 0 smells, 0 nitpicks

## Defects

None found.

## Smells

None found.

## Nitpicks

None found.

## Not found

Correctness: the restart checkpoint precedes the edited block, a minimum-height row either fits its first fragment or moves, and a keep-with-next chain accounts for its ending block.

Contract: the PDF background, gradient defaults, presentation shape constructors, and SmartArt text changes match the approved contribution wave. The ToUnicode hash change is isolated in its own commit.

Panics: new production indexing is bounded by the block and line counts. No new untrusted-input unwrap or expect was found.

OOXML: picture geometry and connector style follow their schema siblings. Raw second paragraph properties and missing gradient attributes survive round trips.

Tests: focused regressions assert observable output and would fail against the prior constructor, resolver, parser, layout, and PDF behavior. The picture geometry regression was observed failing before replay.

Structure: no new trait, generic parameter, module, crate, or forwarding wrapper was introduced.
