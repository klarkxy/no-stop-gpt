# Investigation playbook

Use for broad coverage or a focused candidate whose consumers are difficult to
establish. Apply [Sweep](sweep.md) scope and the common rules in [SKILL.md](../SKILL.md).

## Cover the requested surface

Partition broad work by responsibility rather than file extension. Depending on
the repository, relevant domains include entrypoints, public APIs, configuration,
domain state, persistence, protocols, dynamic loading, background execution,
resource ownership, packages, tests, and operational documentation.

For each in-scope domain, identify the paths examined and meaningful blind spots.
Focused work needs only its affected boundary and consumers. Read history or
additional documents when they could resolve a current uncertainty, not as a
mandatory prelude to every finding.

If authorized parallel work is already in use, give evidence contributors bounded
domains and integrate findings by contract and owner. Assess the evidence behind
material claims; do not accept a search count as a liveness conclusion or duplicate
all investigation when a precise, current trace already establishes the fact.

## Find maintenance burdens

Useful leads include:

- dormant exports, hooks, options, commands, and fields;
- duplicate representations that must remain synchronized;
- flexibility with no current product or operational purpose;
- forwarding layers that neither reduce coupling nor establish a boundary;
- parallel state machines describing the same transition;
- repeated validation or copying within an already maintained guarantee;
- custom infrastructure duplicated by a suitable platform facility;
- support artifacts remaining after their feature has actually retired.

Similarity is not redundancy. Separate implementations can protect different
owners, failure windows, compatibility requirements, or independent checks.

## Distinguish evidence strength

Visible complexity and analyzer results are leads. Consumer traces explain use;
contract evidence establishes obligations; targeted observations support behavior
for the cases exercised. These are evidence categories, not mandatory stages.

Inspect relevant callers and classify hits by their actual role:

- **Runtime:** entrypoints, operational config, loaders, migrations, and shipped code.
- **Support:** tests, comments, documentation, snapshots, and illustrative examples.
- **Uncertain:** public exports, registrations, reflection, generated code,
  examples that might run operationally, and external package consumers.

Support artifacts can document real requirements but cannot create independent
product justification merely by referring to each other. Search alternate call
forms, registration keys, serialized fields, or manifests when the system uses them.
Read surrounding flow rather than inferring semantics from hit counts.

Resolve uncertain use through the relevant loader, publication boundary, release
history, compatibility policy, or downstream evidence within scope. If external
use cannot be bounded, report that limitation rather than calling the surface dead.

Use [ablation](defensive-audit.md#6-use-ablation-when-it-can-resolve-uncertainty)
only if an experiment can answer the remaining question. A passing suite does
not prove that an unexercised or external consumer is absent.

## Consult history where it matters

Use decisions, commits, issues, and comments to identify the original requirement,
whether its conditions still exist, and the present owner of the decision. An old
date does not prove obsolescence. Preserve rejected alternatives when they still
explain a useful constraint. Follow [decision-records.md](decision-records.md) if
record consolidation is part of the authorized change.

## Compare replacement dependencies

Compare contract semantics and residual behavior before choosing a substitute.
Consider platform support, maintenance, dependencies, migration cost, and the local
policy that would remain. Prefer an existing suitable facility when it actually
reduces ownership. A wrapper retaining most custom behavior may simply relocate complexity.

## Rank without optimizing for deletion

Explain confidence separately from maintenance benefit. Weigh consequence,
reversibility, effort, and available checks without inventing a score. A smaller
supported cut can be preferable to a valuable but uncertain one. Preserve important
rejected findings and name the fact needed for unresolved choices.

Complete the requested coverage, not a target number of findings or deleted lines.
