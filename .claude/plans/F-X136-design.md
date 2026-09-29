# F-X136, Fix table row breaks and footer-only pages

**Status**: completed
**Sprint**: S75
**Size**: L
**Depends on**: F-X135

## Problem

Issue 138 reports a 53-page private document with two footer-only pages. Its
smallest reported prefix ends a table on page seven, places the required empty
paragraph alone on page eight, then begins a page-break-before heading. Word
and LibreOffice move the table break earlier. The private document is not
available, so the exact producer package cannot be used as a fixture.

The current row loop in `crates/rdocx-layout/src/paginator.rs:801` places a
whole row whenever its measured height fits and moves a whole row otherwise.
It has no default row-fragment path. `crates/rdocx-layout/src/table.rs:247`
does not carry the authored `w:cantSplit` toggle into `TableRow`, and
`docs/hld/08-rendering-spec.md`, "Tables", explicitly leaves that layout
policy unimplemented. `restore_word_line_heights` in
`crates/rdocx-layout/src/convert.rs:137` supplies the line advances that
accumulate into a row's measured height. F-X135 already corrected paragraph
spacing, cell margins, and horizontal border bands, but did not settle these
two remaining mechanisms.

## Spec reference

- `docs/hld/08-rendering-spec.md`, "Tables", for cell margins, row height,
  border bands, repeated headers, vertical merges, and the current split limit.
- `docs/hld/08-rendering-spec.md`, "Caller-width Word block measurement", for
  row height outside pagination.
- `docs/hld/08-rendering-spec.md`, "The section character grid", for line
  advance and exact-spacing precedence.
- `docs/hld/10-bindings-spec.md`, "Native Word facade stability", for the
  pre-1.0 Rust source impact of one added public row policy field.
- `docs/hld/12-testing-strategy.md`, "Test taxonomy", "The hash harness",
  and "The golden-PNG gate".
- `docs/hld/14-development-backlog.md`, "F-X136, Fix table row breaks and
  footer-only pages".
- Issue 138, including its 37-child and 38-child page-boundary measurements.

## Approach

First build source-created table fixtures that separate cell line advance from
border and margin height, a row that fits only when measured correctly, a row
that must fragment, and a following empty paragraph plus a
page-break-before heading. Compare the deterministic layout's per-line and
per-row geometry before changing it. Correct only a demonstrated Word line
advance error, without changing explicit `exact` or character-grid rules.
The pinned LibreOffice comparison uses explicit 16 pt line spacing because
its host font substitution gives different natural Calibri advances from our
bundled deterministic font. The natural-spacing variant remains a separate
regression and is not forced to match an unlike font environment.

Carry `w:cantSplit` into the existing lowered row representation. This is a
pre-1.0 public `rdocx-layout` struct-field addition, so external struct
literals must name it. The authored split policy already has a real consumer
in the table facade. No new trait, generic, crate, module, or feature flag is
needed.

For a normal flowed table, paginate an automatic or minimum-height row over
one or more page fragments when its content cannot fit in the remaining body
band. Select breaks at cell-content line or block boundaries. Place each
source line once, keep cell and table provenance on every occupied page, and
repeat only the leading header rows on a continuation page. Draw continuation
fill and vertical edges without inventing an interior top or bottom table
border. Respect `w:cantSplit` by moving a row whole if it fits the next page.
If an unsplittable row is taller than a fresh page, make bounded forward
progress with the existing visible-overflow behavior rather than looping.
Keep exact-height clipping, floating tables, and existing vertical-merge
ownership intact. A case the first fragmenter cannot safely divide remains
whole, rather than silently losing or duplicating text. Pagination currently
has no diagnostic return channel, so the fallback is explicitly documented
in the HLD instead of inventing a side-channel message for this story.

Check the source-built pagination boundary against pinned LibreOffice
26.2.5.2. On our side use deterministic bundled fonts. Rasterise at 72 DPI
with pinned Poppler 26.01.0 and compare body-ink occupancy outside the footer,
not entire-page pixels. The tolerance is a 0.1 percent body-ink floor for a
nonempty page. The pinned 72 DPI render measured the heading-only fourth page
at 0.16 percent, so the initial 0.2 percent proposal would reject legitimate
single-line content. Require exact page membership for the fixture's tagged body
lines. Record that Word and the private 53-page document were unavailable,
so the test proves the isolated mechanism, not an unseen byte-for-byte case.

## Rejected alternatives

- Suppressing empty paragraphs before a page-break-before heading would hide
  the page but change authored content and fail other page boundaries.
- Subtracting a fixed number of points from every table row would make one
  fixture pass while breaking fonts, explicit heights, and grid spacing.
- Moving every row whole preserves the current defect for a single row taller
  than a page and leaves `w:cantSplit` without meaning.
- Treating the private reporter document as a passing oracle is impossible
  without its bytes. The source-built boundary and pinned public tools are
  the repeatable evidence available here.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| regression | `table_row_breaks_before_footer_only_page_and_repeats_header` | A source-built repeated-header table, trailing empty paragraph, and page-break-before heading have no footer-only page. Each tagged body line appears once and the header repeats on the continuation page. |
| unit | `table_cell_line_advance_matches_measured_row_height` | Natural and explicit line advances produce the row height implied by measured cell content, margins, and border bands. |
| integration | `table_row_split_policy_respects_cant_split_and_exact_height` | Default rows fragment, `cantSplit` rows move whole when possible, exact-height rows retain clipping, and taller-than-page rows make forward progress. |
| integration | `table_row_fragments_preserve_merge_and_body_ownership` | Continuation fragments retain one logical cell owner and page-scoped body fragments without duplicate or missing text. |
| differential | `table_row_page_membership_matches_pinned_libreoffice` | With LibreOffice 26.2.5.2 and Poppler 26.01.0, tagged lines have the same page membership and no page falls below the revised 0.1 percent body-ink floor. |
| golden | hash and golden-PNG checks | Existing samples remain stable unless a reviewed, exactly enumerated rendering delta is approved in a plan revision. |

The **test gate** is the named
`table_row_breaks_before_footer_only_page_and_repeats_header` regression.
Add the tests to existing test entrypoints, with no binary fixture or new
source file. The complete workspace gate runs once after F-X136 joins F-X135.

## HLD impact

- `docs/hld/08-rendering-spec.md`
- `docs/hld/10-bindings-spec.md`
- `docs/hld/12-testing-strategy.md`
- `docs/hld/14-development-backlog.md`

## Risk routing

- **Layout, pagination, line breaking, and text shaping**. Follow
  `docs/hld/08-rendering-spec.md`. Use deterministic font mode for all
  baselines, run the hash and golden harnesses, and label any intentional
  output delta separately.
- **Public API of a published crate**. Follow `docs/hld/10-bindings-spec.md`
  and the structural rules. State the pre-1.0 `TableRow` source impact,
  run warnings-denied rustdoc and the locally patched 22-package dry run,
  and assert every archive is below 10 MiB.
- **An external oracle comparison**. Follow
  `.claude/skills/differential-testing.md`. Pin LibreOffice 26.2.5.2 and
  Poppler 26.01.0, use deterministic fonts and the stated body-ink tolerance,
  and record that Microsoft Word was not available locally.

No parser, serializer, unit-conversion, theme-colour, dependency-graph,
bundled-font, feature-flag, release-script, or file-move row is triggered.

## Hash harness

The initial expectation is no change to the seven sample outputs, because the
acceptance fixture has a table row at a page boundary that the samples do
not. If a measured line-advance correction changes an existing sample, stop
before recording a baseline. Revise this section with the exact changed
entries and justification, then land the intentional delta in its own
labelled commit. OOXML parts are expected to remain byte-identical.

## Implementation checklist

- [x] Create source-built row-measurement and failing footer-only-page tests.
- [x] Verify the line-advance cause against the fixture before changing it.
- [x] Carry `w:cantSplit` into the existing lowered row and update struct users.
- [x] Implement bounded page fragments for default splittable rows.
- [x] Preserve repeated headers, borders, cell content, merge ownership, and body fragments.
- [x] Compare the fixture with pinned LibreOffice and document the unavailable Word and private-file limits.
- [x] Run focused tests, microscope, `/verify --scoped F-X136`, and all risk riders.

## Open questions

None blocking. The private document is unavailable by the reporter's stated
constraint. Source-built fixtures and the pinned LibreOffice comparison are
the repeatable acceptance evidence, without claiming to reproduce the exact
53-page package.
