# F-X143, Revision listing and CLI story contribution wave

**Status**: completed
**Sprint**: S77
**Size**: M
**Depends on**: F-X139, F-X141

## Problem

`Document::revisions` at `crates/rdocx/src/document.rs:14943` lists the typed
main story, while revision resolution and comparison reach related stories.
The CLI listing at `crates/rdocx-cli/src/commands.rs:691` uses that narrower
view. CLI validation at `crates/rdocx-cli/src/commands.rs:1276` can miss
malformed related XML and undefined style references. PRs 186 and 204 address
these gaps, with PR 204 stacked on S76 PR 198.

## Spec reference

- `docs/hld/03-architecture.md`, "Facade conventions" and the revision
  traversal and mutation paragraphs in "What stays put".
- `docs/hld/04-opc-and-packaging.md`, "The package" and its Word story
  discovery paragraphs.
- `docs/hld/10-bindings-spec.md`, "Python API shape", "Native Word facade
  stability" and "CLIs".
- `docs/hld/12-testing-strategy.md`, "Binding tests" and "The hash harness".

## Approach

After F-X141 completes, review the incremental PR 186 diff and PR 204's
incremental diff after PR 198. Reconcile PR 186's `rdocx-py` and native facade
changes with F-X141. Add owned `StoryRevision` snapshots through
`Document::story_revisions() -> Result<Vec<StoryRevision>>`, whose story IDs
and count agree with supported resolution. Widen Python `Document.revisions`
and CLI revision listing to expose those stories. Preserve existing CLI
schema keys while adding per-story counts and identity, and set scope to
`all-supported-stories`. Keep `main_story_revisions` with its existing
meaning. A revision that resolution reaches but no story owns is an error.
Extend CLI text and Markdown or HTML conversion through the existing story
inventory. Text and conversions keep the body with one named warning if a
related story cannot be read. Validation rejects malformed related XML and
undefined style references, including current Rust-generated samples with
missing definitions. Replay only each PR's unique behavior and tests.

The Rust facade addition is additive for the pre-1.0 crate. Python revision
listing and CLI output change for non-main stories. Preserve existing fields
and text columns while adding story data.

## Rejected alternatives

- Reparse the main typed document for all revision listing. It omits related
  parts and may disagree with resolution.
- Cherry-pick all of PR 204's stacked history. PR 198 is already in S76.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| integration | Existing `rdocx` regression entrypoint, story revision cases | Footer, note, comment and text-box listing names the owner and matches accept or reject counts after save and reopen. |
| integration | Existing CLI entrypoint, revision and compare cases | JSON and text expose every supported story with stable existing fields. |
| integration | Existing CLI entrypoint, text and validation cases | Story text and conversions include supported parts. Malformed related XML and undefined style IDs fail validation with a named part. |
| integration | Existing Python tests and typing smoke | Python revision story identity agrees with native values and the stub. |

**Test gate**: integration, as stated in the backlog. Run focused `rdocx`,
`rdocx-py` and `rdocx-cli` checks, then the scoped gate.

## HLD impact

- `docs/hld/03-architecture.md`
- `docs/hld/04-opc-and-packaging.md`
- `docs/hld/10-bindings-spec.md`
- `docs/hld/12-testing-strategy.md`

## Risk routing

- Parser or serialiser: read `docs/hld/04-opc-and-packaging.md` and
  `06-presentationml-model.md`. Check schema child order, prefix-tolerant
  parsing and byte-preserving unmodelled subtree round-trip.
- Crate dependency graph: read `docs/hld/03-architecture.md`. Check that the
  added CLI XML parser dependency creates no reverse family edge.
- Public API of a published crate: read `docs/hld/10-bindings-spec.md`, state
  semver impact, run `cargo publish --dry-run` for touched published crates
  and check the `.crate` size ceiling.
- PyO3 bindings: read `docs/hld/10-bindings-spec.md`. Run the WASM target
  checks and workspace tests with both Python binding crates excluded.

## Hash harness

Expected unchanged after F-X140 and F-X141's distinct reviewed baseline
updates.

## Implementation checklist

- [x] Review PR 186 and the PR 204 incremental diff against S76 PR 198.
- [x] Reconcile native and Python overlap with completed F-X141.
- [x] Make revision listing agree with supported resolution and story IDs.
- [x] Cover story text, related-part validation and style validation.
- [x] Run focused checks, risk riders and microscope to zero findings.

## Open questions

None. CLI schema 1 gains story fields and all-story scope. Text and
conversion retain the body with one named warning for malformed related
parts, while validation fails on malformed parts and undefined style IDs.
