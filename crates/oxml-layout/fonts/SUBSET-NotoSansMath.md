# Noto Sans Math symbol subset

Source: `NotoSansMath-Regular.ttf` (Noto Sans Math Regular, version 3.0),
as shipped in the `noto-fonts` package.

Source SHA-256:
`d51afd5739c7ba6c44fcab35a88160e25dfb69a2d4ad0bd99533f8d894af1f96`

Output SHA-256:
`08e6b22cbcdf80e7f429f605522c8fddfd5156c07cc351ecf73d459a4f8618b6`

Purpose: coverage fallback for symbols that Carlito / Caladea / Liberation do
not carry (set membership, arrows, letterlike ℝ ℕ ℤ, mathematical alphanumerics).
It is never selected by family name; `FontManager::resolve_font_for_text`
picks it only for characters the requested face cannot draw.

Repertoire: space, degree/plus-minus, middle dot, multiplication/division
signs, Greek and Coptic, general punctuation (dashes, quotes, ellipsis,
primes), fraction slash, euro, Letterlike Symbols, Number Forms, Arrows,
Mathematical Operators, Miscellaneous Technical, Geometric Shapes, a few
miscellaneous symbols and card suits, Miscellaneous Mathematical Symbols-A/B,
Supplemental Arrows-A, Supplemental Mathematical Operators, and Mathematical
Alphanumeric Symbols. Reproduce it with FontTools:

```text
pyftsubset NotoSansMath-Regular.ttf --output-file=NotoSansMath-symbols-subset.ttf --unicodes=U+0020,U+00B0-00B1,U+00B7,U+00D7,U+00F7,U+0370-03FF,U+2010-2027,U+2032-2037,U+2044,U+20AC,U+2100-214F,U+2150-218F,U+2190-21FF,U+2200-22FF,U+2300-23FF,U+25A0-25FF,U+2600-2604,U+2660-2667,U+27C0-27EF,U+27F0-27FF,U+2980-29FF,U+2A00-2AFF,U+1D400-1D7FF --glyph-names --symbol-cmap --legacy-cmap --notdef-glyph --notdef-outline --recommended-glyphs --name-IDs=* --name-legacy --name-languages=* --layout-features=* --no-hinting
```
