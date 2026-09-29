# F-X135, all aspects, pass 1

**Reviewed**: commits after `01ea157d` through `8f9d5b69`, 64 files with
11,738 insertions and 565 deletions
**Verdict**: 4 defects, 0 smells, 0 nitpicks

## Defects

### D1, relationship remapping leaves the edited package graph inconsistent

`crates/rdocx/src/comparison.rs:639`

The comparison remaps relationship identifiers in the edited owner XML and
stores only that XML. It does not rename the corresponding entries in the
edited owner's `.rels` collection. If an equivalent relationship moves from
`rId2` to the original document's `rId1`, the edited XML now names `rId1`
while its payload still belongs to `rId2`. An existing unrelated `rId1` makes
normalization resolve the wrong target, and an absent `rId1` leaves a dangling
reference. The staged comparison input must keep owner XML and relationship
identifiers consistent.

### D2, leaf-byte equality is not relationship-graph equality

`crates/rdocx/src/comparison.rs:622`

Every internal relationship type is treated as equivalent when its immediate
target bytes match. A header, chart, or other relationship-bearing part can
have identical XML bytes while its own relationships resolve different images
or embedded data. Remapping that owner relationship then hides a real edited
package change. The mapping must either be limited to proved leaf types needed
by the picture report or compare the complete reachable relationship graph.

### D3, the compact diagnostic assertions never execute on the passing path

`crates/rdocx/tests/regression_test.rs:30067`

The test inspects the diagnostic only when `compare` returns an error and then
unconditionally requires the same result to succeed. A successful regression
therefore proves none of the contract that a forced postcondition failure names
one story and item without dumping `CT_Drawing`. A separate forced mismatch or
unit test must exercise `first_normalized_mismatch` and the resulting message.

### D4, the DOCX preservation gate does not compare every package entry

`crates/rdocx/tests/regression_test.rs:30166`

The no-op half checks a selected list of modeled parts and two relationship
parts rather than the complete ZIP entry map promised by the test name and
design contract. The targeted-edit half iterates only source entries at line
30178, so a newly added output entry is invisible. Comparing complete maps for
the no-op case and symmetric key sets for the edit case is required to prove
that no unrelated part was rewritten, added, or removed.

## Smells

None.

## Nitpicks

None.

## Not found

Panics produced no additional findings. New production indexing is preceded by
bounded counts or validated paths, and fixed canonical XML is the only new
production `expect` input.

OOXML produced no additional findings. The adopted writers retain schema child
order, namespace-aware parsing, and raw unsupported content.

Structure produced no findings. The new Python modules are the user-authorized
PR modules, and the diff adds no speculative trait, generic, crate, or feature
flag.
