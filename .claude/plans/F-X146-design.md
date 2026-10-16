# F-X146, Word line height and inline picture spacing

**Status**: completed
**Sprint**: S79
**Size**: L
**Depends on**: F-X140

## Problem

`crates/rdocx-layout/src/convert.rs:137` scales the whole natural line height for proportional Word spacing. A tall inline picture is therefore scaled with the text. The same function uses generic ascent and descent and falls back to 12 points for an empty line. `crates/oxml-layout/src/line.rs` and the tab conversion path also retain the line break and tab placement defects addressed by assigned PRs 222 and 237.

## Spec reference

- `docs/hld/08-rendering-spec.md`, "Text in a shape" and "Word render fidelity gate" geometry as described in `docs/hld/12-testing-strategy.md`, "The Word render fidelity gate".
- `docs/hld/03-architecture.md`, the Word layout conversion and shared layout boundary.
- `docs/hld/14-development-backlog.md`, "F-X146, Word line height and inline picture spacing".

## Approach

Review the incremental diffs of PRs 222, 225 and 237 against their actual parents, then replay compatible changes on the S78 prefix. Keep PowerPoint line pitch and shared rich-line breaking in `oxml-layout` and its existing consumers. Resolve Word line ascent, descent and leading with bundled font metrics before applying proportional spacing to text height only. Preserve inline picture height, mark metrics for picture-only and empty lines, and the existing exact and at-least rules. Reconcile PR 237's tab stops, numbered TOC entry and page-field alignment with the shared line path. Keep the Word rich-line symptom of Issue 226 within this story and leave its plain-line symptom to F-X163. Review every public field addition for compatibility and keep behavioural hash changes in labelled commits.

Carry page-field tab alignment through pagination as
`oxml_layout::TabAlignedField { start: f64, end: f64, shift: f64, gap: f64 }`
on `GlyphRun::tab_aligned: Option<TabAlignedField>`. Add
`LineBreakParams::clamp_tabs_past_margin: bool` and
`rdocx_layout::LayoutInput::clamp_tabs_past_margin: bool` for the Word
compatibility-mode rule. These are additive public surface changes in
pre-1 crates, but callers constructing the public structs with literals must
supply the new fields. Existing literal callers in this workspace must be
updated. The new value type carries measured geometry across the existing
layout and PDF boundary, with no new trait or crate.

## Rejected alternatives

- Applying the PR heads as whole branch merges would bring their old bases and overlapping geometry decisions without an incremental review.
- Scaling picture height with `w:line` would retain the Issue 162 defect.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| golden | Four-family, two-size, two-spacing pitch matrix | Bundled-font Word pitch matches pinned measurements at 240 and 264 spacing. |
| golden | Word-exported inline picture fixture | Picture height is stable, the figure and caption paginate together, and the page count matches Word. |
| regression | PR 222 rich-line and PowerPoint pitch cases | Rich lines use UAX 14 breaks and PowerPoint percentage spacing keeps its measured pitch. |
| regression | PR 237 tab and TOC cases | Stops, numbered entries and page fields align with the pinned Word positions. |
| gate | Backlog golden test gate | The four-family, two-size, two-spacing pitch matrix and the Word-exported picture fixture meet pinned expected geometry. |

## HLD impact

- `docs/hld/03-architecture.md`
- `docs/hld/08-rendering-spec.md`
- `docs/hld/12-testing-strategy.md`

## Risk routing

- Unit conversion and `Twips`: read `docs/hld/01-glossary.md` units and the `CLAUDE.md` deliberate truncation rule. Preserve truncating constructors and declare every geometry-driven harness delta.
- Layout, pagination, line breaking and text shaping: read `docs/hld/08-rendering-spec.md`. Run deterministic bundled-font geometry and golden checks, and re-record only reviewed baselines.
- TOC serialisation: read `docs/hld/04-opc-and-packaging.md` and `06-presentationml-model.md`. Check schema child order and retained XML in a round trip.
- Public API of published crates: read `docs/hld/10-bindings-spec.md` and the `CLAUDE.md` structural rules. State the additive field compatibility impact, run `cargo publish --dry-run` and assert package sizes for touched published crates.
- External Word and PowerPoint oracle comparison: read `.claude/skills/differential-testing.md`, pin the oracle versions and record them.

## Hash harness

Expected reviewed rendering deltas from PRs 225 and 237. PR 225 changes Word line heights and three sample page counts. PR 237 changes list and tab positions in six samples. PR 222 expects the Word hash samples unchanged. Record each intentional delta in its own labelled commit and reconcile the combined baseline before completion.

## Implementation checklist

- [x] Review each assigned PR's incremental diff and overlap with the S78 prefix.
- [x] Add failing deterministic line, picture, rich-line, tab and TOC regression cases to existing test entrypoints.
- [x] Reconcile shared layout and Word conversion changes against the tests.
- [x] Run focused layout and rendering gates, pin oracle evidence, and account for each hash and pixel delta.
- [x] Run scoped verification and a zero-finding microscope.

## Open questions

None. The backlog assigns all three PRs and separates Issue 226's remaining plain-line symptom into F-X163.
