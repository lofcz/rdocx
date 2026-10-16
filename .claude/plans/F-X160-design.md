# F-X160, Tolerant style, drawing and measurement reads

**Status**: completed
**Sprint**: S83
**Size**: L
**Depends on**: F-X159, F-X147

## Problem

Whole-graph style validation rejects edits to producer files that already
contain repeated style IDs or multiple table defaults
(`crates/rdocx/src/document.rs:18049`). Drawing identity scanning refuses a
repeated `wp:docPr/@id` within one part
(`crates/rdocx/src/document.rs:3716`). Several modeled integer measurements
reject decimal lexical values accepted by Word and other producers
(`crates/rdocx-oxml/src/document.rs:637`).

## Spec reference

- `docs/hld/04-opc-and-packaging.md`, "The package" and "Package integrity".
- `docs/hld/10-bindings-spec.md`, "Native Word facade stability" and "CLIs".
- `docs/hld/12-testing-strategy.md`, "Binding tests" and "The Word corpus".
- `docs/hld/01-glossary.md`, units.

## Approach

Review PRs 248, 249 and 250 against the completed F-X159 prefix. Resolve an
existing duplicate style ID to its first definition for edits, retain old graph
defects but reject newly introduced defects, and cover every style mutation.
Count producer drawing IDs per part, reserve a fresh ID for authored content,
and reject any increase in duplicate occurrences after staged edits. Parse
modeled decimal integer measurements with exact decimal arithmetic, rounding
half away from zero while retaining untouched source bytes. Keep malformed and
out-of-range values as contextual errors.

## Rejected alternatives

- Normalize producer styles or drawing IDs on open. This changes untouched
  packages and breaks provenance.
- Parse measurements with floating point. Exact half cases and bounds need
  decimal digit arithmetic.

## Test plan

| Category | Test | Asserts |
|---|---|---|
| regression | Both Issue 243 reporter fixtures through all style mutators | First definition and default authoritative, old defects retained, new defects rejected |
| regression | Issue 247 fixture through native, CLI and Python reads and edits | Repeated source IDs accepted, authored IDs fresh, no-op bytes preserved, save and reopen |
| regression | Issue 246 fixture through native and CLI reads and edits | All modeled measurement sites round exact halves, errors name element and attribute, untouched bytes retained |
| integration | Existing Word package and binding test binaries | Read, edit, save and reopen with package graph valid |
| harness | `python3 scripts/hash_harness.py --check` | No additional baseline delta after F-X159 |

The backlog test gate is regression and includes both Issue 243 reporter cases.

## HLD impact

- `docs/hld/04-opc-and-packaging.md`
- `docs/hld/10-bindings-spec.md`
- `docs/hld/12-testing-strategy.md`

## Risk routing

- Unit conversion: read `docs/hld/01-glossary.md` and `CLAUDE.md`. Existing
  constructors keep truncating. Check exact positive and negative halves and
  declare any harness delta.
- Parser or serializer: read `docs/hld/04-opc-and-packaging.md` and
  `docs/hld/06-presentationml-model.md`. Check schema order and untouched raw
  subtree preservation.
- Public facade behavior and Python: read `docs/hld/10-bindings-spec.md`.
  Check semver impact, `cargo publish --dry-run`, crate size and WASM target.
- External oracle: read `.claude/skills/differential-testing.md`. Pin every
  oracle version used for reporter fixtures.

## Hash harness

Unchanged relative to the completed F-X159 prefix. All three changes affect
reads or mutations of producer input outside the deterministic samples.

## Implementation checklist

- [x] Review PRs 248, 249 and 250 for scope, conflicts and missing coverage.
- [x] Run both Issue 243 fixtures through every affected style mutator.
- [x] Cover duplicate drawing IDs and decimal measurements through read, edit,
  save and reopen across native, CLI and Python paths.
- [x] Run focused tests, risk riders, scoped verify and microscope to zero.

## Open questions

None. The sprint definition fixes the producer inputs and accepted behavior.
