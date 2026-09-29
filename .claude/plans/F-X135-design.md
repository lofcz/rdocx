# F-X135, Integrate PRs 146 through 151 and resolve unassigned reports

**Status**: completed
**Sprint**: S75
**Size**: L
**Depends on**: F-X134

## Problem

Contributor PRs 146 through 151 are based on S74 and are individually
mergeable, but five still report a failing hosted Python job caused by the
F-X134 defect on their base. PR 148 has a separate failure because its intended
layout change updates the hash harness but not the deterministic golden-PNG
baseline. The branches overlap in the PowerPoint facade, Python bindings,
tests, HLD, and package measurements, so merging them independently would leave
conflicts and stale archive rows.

Open Issues 134, 135, 136, 139, and 140 initially had no implementation pull
request. They cover comparison formatting loss with revision identities, an empty
paragraph-property shell, an acceptance failure with reordered drawing and
text paragraphs, authored `nil` border normalization, and unnecessary rewrites
of unchanged modeled and relationship parts. Each is reproducible on S74 and
remains open after the six contributor branches.

## Spec reference

- `docs/hld/03-architecture.md`, "The dependency rule", "Crate-level conventions", and "Facade conventions".
- `docs/hld/04-opc-and-packaging.md`, "The package", "What transfers unmodified", "Relationship types", and "Package integrity".
- `docs/hld/05-drawingml-model.md`, "Text body" and "Preservation".
- `docs/hld/06-presentationml-model.md`, "Public facade", "Notes parts", "The shape tree", "Preservation strategy", and "Validation".
- `docs/hld/08-rendering-spec.md`, "Text in a shape", "Tables", "Autofit", and "Performance".
- `docs/hld/10-bindings-spec.md`, "The PyO3 lifetime problem", "Python API shape", "Native PowerPoint collaboration and navigation", "Packaging", "CI", and "CLIs".
- `docs/hld/12-testing-strategy.md`, "Test taxonomy", "The hash harness", "The golden-PNG gate", "Binding tests", and "What CI runs".
- `docs/hld/14-development-backlog.md`, "F-X135, Integrate PRs 146 through 151 and resolve unassigned reports".
- `docs/hld/15-build-and-toolchain.md`, "Deterministic rendering", "Packaging", and "CI job matrix".
- PR heads `3f6bfea2`, `3c668b1b`, `1acfb2f4`, `37253c28`, `264c60f0`, and `29f43de7` for PRs 146 through 151.

## Approach

Adopt the focused behavior commits from PRs 146 through 151 in numeric order,
then reconcile the overlapping PowerPoint facade, Python module exports,
stubs, tests, HLD, and README surfaces against all six contributor contracts.
Drop each branch's terminal package-measurement commit and derive one combined
measurement after the integrated source is final. Retain PR 148's separately
labelled hash-baseline commit, reproduce its current golden-PNG failure, and
record the three intentional raster changes in a separate labelled baseline
commit.

Resolve the five reports without a pull request in the same integration
boundary. Comparison will ignore producer root-attribute carriers when
choosing its paragraph algorithm, treat an empty modeled property shell as no
formatting, keep drawing relationship semantics in acceptance normalization,
and report the first mismatching story and item without raw model dumps. OPC
relationships will retain their original bytes while their semantic item list
is unchanged. DOCX and PPTX staging will serialize only modeled parts whose
typed state changed. Authored `nil` and `none` borders remain semantically
equivalent while retaining the source token through an unrelated edit.

Do not implement the remaining Issue 138 line-height or row-splitting work in
this story. PR 148 explicitly identifies both as outstanding, so Issue 138
remains open with a precise integration comment. PR 152 is represented by the
completed F-X134 implementation rather than applied a second time.

PRs 153 and 154 arrived after the initial issue fixes. Compare their tests and
implementations with this integrated branch. Retain PR 153's attribute-free
empty-property parsing and unmodelled-property diagnostics where they close
gaps. The branch already models `nil` as a distinct border token, so PR 154's
additional spelling field is redundant. Keep the existing token model and add
its direct reserialization regression.

PR 147's `crates/rpptx-py/src/layout.rs`, PR 149's Python `dml` and text enum
modules, and PR 150's Python `dml` and shape enum modules are explicitly
authorized by the user's request to integrate those branches. No new crate,
feature flag, trait, generic parameter, or dynamic dispatch is introduced.

## Rejected alternatives

- Merging every PR directly to `main` is forbidden because only
  `/close-sprint` may merge the reviewed sprint to `main`.
- Keeping each branch's archive-measurement commit would record incompatible
  intermediate package sizes rather than the integrated package set.
- Closing Issue 138 after PR 148 would claim completion while its documented
  line-height and row-splitting gaps remain.
- Suppressing comparison acceptance checks would hide real package loss rather
  than fixing Issues 134 through 136.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| regression | contributor branch test inventory | Every test added by PRs 146 through 151 remains present and passes after conflict reconciliation. |
| golden | `golden_png_harness.py --check` | PR 148's three intended deterministic raster changes are reviewed and no other sample moves. |
| regression | `comparison_tracks_paragraph_properties_with_revision_identities` | `w:rsidR`, `w:rsidRDefault`, `w:rsidP`, and the common four-attribute set do not suppress `w:pPrChange`. |
| regression | `comparison_treats_empty_paragraph_properties_as_absent` | Empty `w:pPr` compares like no paragraph properties and does not block a real formatting revision. |
| regression | `reordered_drawing_paragraphs_accept_without_model_dump` | A source-built relationship-backed picture and text paragraph can exchange order, and any forced postcondition diagnostic names one story and item without raw drawing bytes. |
| round-trip | `nil_border_tokens_survive_unrelated_document_edits` | Body table borders authored as `nil` remain `nil` after an unrelated document mutation and reopen with invisible-border semantics. |
| regression | `no_op_save_preserves_every_unchanged_part` | DOCX and PPTX saves retain exact bytes for unchanged modeled parts and `.rels` entries, while a targeted edit rewrites only its owned part and graph edges. |
| regression | `empty_paragraph_properties_model_only_attribute_free_form`, `unmodelled_property_changes_report_a_diagnostic`, `nil_border_spelling_survives_reserialization` | PRs 153 and 154 do not expose a remaining parser, diagnostic, or border-token gap in the integrated result. |
| differential | complete Python binding suites | rpptx matches pinned python-pptx 1.0.2 for the contributed object model, and rdocx keeps its unchanged linear timing bound. |

The **test gate** is the named `no_op_save_preserves_every_unchanged_part`
regression. The complete contributor test inventory, both Python suites, the
scoped feature gate, hash harness, golden-PNG harness, and package checks are
also mandatory. The full workspace gate runs once over the integrated sprint.

The integrated full gate found one stale M21 portable source hash after
unchanged-part PPTX preservation. Its portable fixture is pinned to the new
deterministic source bytes. The older SHA remains attached to the ignored
historical PowerPoint recording, which is not relabelled as current evidence.

## HLD impact

- `docs/hld/03-architecture.md`
- `docs/hld/04-opc-and-packaging.md`
- `docs/hld/05-drawingml-model.md`
- `docs/hld/06-presentationml-model.md`
- `docs/hld/08-rendering-spec.md`
- `docs/hld/10-bindings-spec.md`
- `docs/hld/12-testing-strategy.md`
- `docs/hld/13-risks-and-open-questions.md`
- `docs/hld/14-development-backlog.md`
- `docs/hld/15-build-and-toolchain.md`

## Risk routing

- **Layout, pagination, line breaking, and text shaping**. Follow
  `docs/hld/08-rendering-spec.md`. Use deterministic font mode, preserve PR
  148's labelled hash delta, and re-record only the three reviewed golden-PNG
  changes.
- **Any parser or serializer**. Follow `docs/hld/04-opc-and-packaging.md` and
  `docs/hld/06-presentationml-model.md`. Test prefix aliases, inherited scope,
  schema order, exact raw-subtree retention, and repeated save and reopen.
- **Public API of a published crate**. Follow `docs/hld/10-bindings-spec.md`
  and the structural rules. The additions are intentional pre-1.0 surface.
  Run rustdoc with warnings denied, inspect the API diff, run every package
  dry-run, and enforce the archive-size limit.
- **WASM or PyO3 bindings**. Follow `docs/hld/10-bindings-spec.md`. Run the
  complete isolated Python 3.12 suites, strict typing and stub checks, and the
  two WASM target checks. Exclude the two binding crates from all-feature
  workspace binaries.
- **A new module or file**. The user explicitly authorized integration of PRs
  147, 149, and 150, whose current reviewed heads contain the named Python
  modules. No speculative module is added.
- **An external oracle comparison**. Follow
  `.claude/skills/differential-testing.md`. Pin python-pptx 1.0.2 and
  LibreOffice 26.2.5.2. Record that Microsoft Word was not available locally
  rather than treating its absence as passing evidence.

No unit-conversion, theme-colour, dependency-graph, bundled-font, feature-flag,
release-script, or file-move row is triggered.

## Hash harness

PR 148 intentionally changes layout and carries a separately labelled baseline
commit. Re-establish its declared spacing and table-border delta on the
integrated tree. Every other adopted PR and every no-PR issue fix is expected
to leave the hash harness unchanged. Any additional output delta blocks
completion.

## Implementation checklist

- [x] Create source-built failing regressions for Issues 134, 135, 136, 139, and 140.
- [x] Adopt PR 146's two behavior commits and reconcile its HLD changes.
- [x] Adopt PR 147's three behavior commits and Python layout surface.
- [x] Adopt PR 148's three behavior commits and labelled hash baseline.
- [x] Re-record only PR 148's reviewed deterministic golden-PNG delta.
- [x] Adopt PR 149 and PR 150 as the two complementary Python authoring halves.
- [x] Adopt PR 151's JSON, notes, and comment command commits.
- [x] Reconcile every overlapping facade, binding, test, HLD, and README edit.
- [x] Fix all five reports without implementation pull requests.
- [x] Derive one final package-measurement update for the integrated source.
- [x] Run the focused gates, scoped verification, risk riders, and both binding suites.
- [x] Record contribution and issue evidence for the close-sprint comments.
- [x] Compare PRs 153 and 154 with the integrated fixes and retain their useful regression cases.

## Open questions

None. The user explicitly requested the open contribution wave, all open issue
reports without a pull request, the remaining S75 stories, GitHub closure of
fully resolved records, and specific human-written thanks to the contributor.
