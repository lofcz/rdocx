# F-X092, correctness, pass 4

**Reviewed**: legacy PDF extraction follow-up, seven implementation, test and
contract files before this review record.
**Verdict**: 0 defects, 0 smells, 0 nitpicks.

## Defects

None.

## Smells

None.

## Nitpicks

None.

## Evidence

- Font mappings use the font cmap rather than shaped-glyph position. Glyph
  widths include ligatures without direct mappings. ActualText preserves the
  original run text, and both run paths share the reviewed transform handling.
- The bundled-font regression extracts ligatures, subsequent prose, Czech
  characters and combining accents exactly. The export corpus preserves
  malformed formula source and the following prose in both DOCX and PDF.
- Poppler 26.01.0: 87 PDF tests and 473 Word tests pass. Seven existing ignored
  tests remain ignored. The prior version checks are unchanged.
- The Word and PowerPoint reading-order fixtures rasterize byte-identically
  through the committed and corrected PDF writers. Their old pins predate the
  merged layout. The application reproduction also has identical page pixels.
- The sample baseline refresh contains 21 intentional PDF fingerprints and two
  stale native PNG entries. No OOXML entry changes. Native PNG generation runs
  before PDF generation and its implementation is unchanged.
- Scoped formatting and clippy checks pass. No public types or dependencies
  were introduced, and the rich-run painting order is unchanged.
