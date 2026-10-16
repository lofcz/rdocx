# F-X156, Presentation slide and table contribution

**Status**: completed
**Sprint**: S81
**Size**: L
**Depends on**: F-X142, F-X155

## Problem

The current presentation facade can add or duplicate a slide inside one deck, but has no checked cross-deck import. Global `try_replace_text` in `crates/rpptx/src/lib.rs` cannot confine a counted edit to one slide or text frame. The S77 built-in table style resolver renders cell regions, while PR 238 adds the table background and additional built-in definitions. PRs 231, 235 and 238 were authored on older bases and overlap the current facade, Python handles, renderer and tests.

## Spec reference

- `docs/hld/04-opc-and-packaging.md`, "Relationship types", "Part naming", "Media" and "Package integrity".
- `docs/hld/05-drawingml-model.md`, "Tables" and "Preservation".
- `docs/hld/06-presentationml-model.md`, "Public facade", "Relationship remapping", "Adding a slide" and "Validation".
- `docs/hld/07-inheritance-and-resolution.md`, "4. Style references" and "The resolver".
- `docs/hld/08-rendering-spec.md`, "Tables".
- `docs/hld/10-bindings-spec.md`, "The PyO3 lifetime problem" and "Python API shape".
- `docs/hld/12-testing-strategy.md`, "The deck corpus" and "Binding tests".
- `docs/hld/14-development-backlog.md`, "F-X156, Presentation slide and table contribution".

## Approach

Review the incremental commits of PRs 231, 235 and 238 and replay accepted behavior on the completed F-X155 prefix. Add `Presentation::import_slide(&mut self, source: &Presentation, index: usize, layout_index: Option<usize>, insert_index: Option<usize>) -> Result<SlideRef<'_>>`, with staged package validation, explicit destination layout selection and checked relationship remapping. Reuse equal destination media, carry notes and supported SmartArt graphs, and reject unsupported internal relationship graphs atomically. Bind the import through the existing Python presentation and slide collection handles. Add counted replacement confined to one slide, its optional notes, or one text frame, retaining revision and expected-count behavior. Extend the existing table style resolver and renderer with the missing background behavior while preserving raw children and schema order. Keep all new tests in existing entrypoints.

The native and Python additions are additive on pre-1.0 surfaces. Unsupported chart, embedded-object, comment and source-deck slide-jump import must return a concrete error with a documented fallback of rebuilding the affected slide in the destination deck.

## Rejected alternatives

- Cherry-picking whole PR heads would replay stale parent changes and overwrite the S77 and S80 facade and binding fixes.
- Copying slide XML without rebinding relationships could produce a package that reopens but silently loses media or notes.
- Treating an unsupported relationship as a lossy import would hide missing content from the caller.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| integration | Existing `rpptx` integration entrypoint, slide import cases | Imported slide order, notes, media and relationships survive save, reopen and `rpptx validate`, while unsupported graphs refuse without mutation. |
| integration | Existing `rpptx-py` documented examples and typing smoke | Python import and scoped counted replacement agree with native results and leave handles valid on a failed expected count. |
| regression | Existing `oxml-drawing` and `rpptx-layout` table tests | Known built-in style IDs resolve table background and cell regions in schema order, while unknown content is preserved. |
| differential | Pinned python-pptx 1.0.2 and LibreOffice 26.2.5.2 | Saved import and table deck reopens and renders within declared deterministic-font tolerance. |
| gate | Backlog integration test gate | Imported and edited decks reopen, validate and render without losing media, notes or relationships. |

## HLD impact

- `docs/hld/04-opc-and-packaging.md`
- `docs/hld/05-drawingml-model.md`
- `docs/hld/06-presentationml-model.md`
- `docs/hld/07-inheritance-and-resolution.md`
- `docs/hld/08-rendering-spec.md`
- `docs/hld/10-bindings-spec.md`

## Risk routing

- Parser or serialiser: read `docs/hld/04-opc-and-packaging.md` and `06-presentationml-model.md`. Check child order, prefix-tolerant reads, fixed-prefix writes and byte-preserving unmodelled subtrees.
- Layout and rendering: read `docs/hld/08-rendering-spec.md`. Render with deterministic bundled fonts and review any baseline delta.
- Public API of published crates: read `docs/hld/10-bindings-spec.md` and `CLAUDE.md` structural rules. State additive pre-1.0 semver impact, run `cargo publish --dry-run` and assert archive size.
- PyO3 bindings: read `docs/hld/10-bindings-spec.md`. Run Python tests and the WASM target check, excluding both Python crates from workspace tests.
- External oracle: read `.claude/skills/differential-testing.md`. Pin python-pptx and LibreOffice versions and record structural and pixel comparisons.

## Hash harness

Expected unchanged. Any intentional output delta requires its own labelled behavioral commit and reviewed expected delta.

## Implementation checklist

- [x] Review PRs 231, 235 and 238 against their incremental parents and current F-X155 code.
- [x] Add failing import, scoped replacement and table style cases in existing test entrypoints.
- [x] Reconcile facade, OXML, resolver, renderer, Python bindings, stubs and documentation.
- [x] Save, reopen, validate and render imported and edited decks with pinned external oracles.
- [x] Run focused checks, risk riders, scoped verification and microscope to zero findings.

## Open questions

None. The contribution's bounded import refusal is explicit in PR 235. Document the fallback and verify atomic refusal.
