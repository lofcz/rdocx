# F-X154, Presentation text and preservation repair

**Status**: completed
**Sprint**: S80
**Size**: M
**Depends on**: F-X142

## Problem

`CT_TextBodyProperties` in `crates/oxml-drawing/src/text/body.rs` models only selected `a:bodyPr` attributes and drops others when an edited slide is serialised. `CT_TextBody::set_text` in `crates/oxml-drawing/src/text/mod.rs` puts assigned line feeds in one `a:t`, and the layout path can reject that producer text. These are Issues 215 and 216 and assigned PRs 218 and 223.

## Spec reference

- `docs/hld/05-drawingml-model.md`, "Text body" and "Preservation".
- `docs/hld/06-presentationml-model.md`, "The shape tree", "Preservation strategy" and "Validation".
- `docs/hld/08-rendering-spec.md`, "Text in a shape" and "Slide text layout as data".
- `docs/hld/12-testing-strategy.md`, "The deck corpus" and "Binding tests".
- `docs/hld/14-development-backlog.md`, "F-X154, Presentation text and preservation repair".

## Approach

Review PRs 218 and 223 as incremental diffs against the integrated prefix, then replay their behaviour without unrelated archive or documentation changes. Keep unmodelled `a:bodyPr` attributes in source order beside typed attributes, including namespace declarations. Split frame and shape assigned text into paragraphs at LF or CRLF and write vertical tab as `a:br`. Keep `Run.text` literal as its existing contract says. Normalize producer line separators at the presentation layout boundary so they cannot send more than one bidi paragraph into one line layout. Preserve formatting and unmodelled children through these edits, and add slide and shape context to remaining layout errors. Validate and reopen edited decks.

## Rejected alternatives

- Modelling every `a:bodyPr` attribute increases the typed surface without a renderer consumer.
- Silently dropping producer line separators during parsing would change saved source text.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| round-trip | Issue 215 unmodelled body properties in existing `oxml-drawing` and `rpptx` entrypoints | Unknown attributes and a namespace declaration survive unchanged after another shape and the owning shape are edited, including a second save. |
| regression | Issue 216 assigned text and producer text in existing entrypoints | LF and CRLF make paragraphs, vertical tab makes a break, and raw run text with separators still lays out and renders. |
| differential | Pinned python-pptx 1.0.2 reader | Paragraph and break structure from the authored deck agrees after reopen. |
| golden | Cross-viewer text layout probe | Deterministic rpptx render and pinned LibreOffice output meet a stated line-position tolerance. |
| gate | Backlog regression test gate | Unedited bodies remain byte-equivalent where required, and assigned line feeds survive layout, validation and rendering. |

## HLD impact

- `docs/hld/05-drawingml-model.md`
- `docs/hld/06-presentationml-model.md`
- `docs/hld/08-rendering-spec.md`

## Risk routing

- Parser and serialiser: read `docs/hld/04-opc-and-packaging.md` and `06-presentationml-model.md`. Check `xsd:sequence`, prefix-tolerant read, fixed-prefix write and byte-preserving retained XML.
- Layout and line breaking: read `docs/hld/08-rendering-spec.md`. Use deterministic bundled fonts for baseline comparisons.
- Public API of `oxml-drawing` if the text setter contract changes: read `docs/hld/10-bindings-spec.md` and `CLAUDE.md` structural rules. State pre-1.0 behaviour impact, run `cargo publish --dry-run` and check `.crate` size.
- PyO3 binding acceptance: read `docs/hld/10-bindings-spec.md`. Run the binding suite, compile WASM targets and exclude binding crates from workspace tests.
- External oracle: read `.claude/skills/differential-testing.md`. Pin python-pptx 1.0.2 and LibreOffice versions, compare structural output and use a stated render tolerance.

## Hash harness

Expected unchanged for single-line harness inputs. Any changed output requires a separate labelled behavioural commit and reviewed expected delta.

## Implementation checklist

- [x] Review PRs 218 and 223 against the current prefix and identify overlap.
- [x] Add Issue 215 and 216 failing cases to existing test entrypoints.
- [x] Retain unknown body property attributes and implement line separator semantics.
- [x] Run focused round-trip, layout, Python, validation and cross-viewer checks.
- [x] Run scoped verification and microscope to zero findings.

## Open questions

None. The assigned issues state the required preservation and text semantics.
