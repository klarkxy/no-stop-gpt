# Sweep: subsystem and repository simplification

Reduce unnecessary concepts and coordination obligations while preserving the
requested behavior. [SKILL.md](../SKILL.md) owns common principles, authority,
and protected outcomes. This reference covers investigation through delivery.

## Scope and outcome

- **Survey:** audit, investigate, or propose simplification. Stay read-only and
  return evidence-backed findings; do not create repository files unless requested.
- **Change:** an explicit request to simplify, remove, consolidate, or apply
  findings authorizes investigation and changes within that scope.
- **Focused:** cover the named boundary and its affected consumers.
- **Broad:** cover the requested repository domains, including blind spots;
  do not stop at the first easy candidate.

Intermediate findings and mode changes do not reset authorization. Hold only cuts
that depend on unresolved material choices, such as retiring supported behavior
or compatibility, and continue independent authorized work.

## Investigate the contract

Read applicable instructions and working-tree state. Consult manifests, test and
generation conventions, architecture, or history where they affect the change.
Distinguish user changes, generated files, and externally maintained surfaces.

For broad work, partition by responsibility: entrypoints and public interfaces,
configuration, domain state, persistence, protocols and dynamic loading, background
work and resource ownership, packages, tests, and operational documentation.
Record the relevant paths examined and meaningful blind spots. A local cleanup
does not need a full repository inventory.

Look for dormant contracts, duplicate state, ownerless flexibility, forwarding
layers without a boundary, parallel state machines, repeated defense, custom
infrastructure with a suitable existing replacement, and support artifacts left
after feature retirement. These are leads, not deletion verdicts.

Trace relevant producers, consumers, and failures; classify matches by actual use:

- **Runtime:** shipped entrypoints, configuration, loaders, and migrations.
- **Support:** tests, comments, documentation, snapshots, and examples.
- **Uncertain:** public exports, registrations, reflection, generated code,
  operational examples, and external consumers.

Search alternate call forms, dispatch keys, serialized fields, and manifests when
the system uses them. Read surrounding flow rather than counting hits. Resolve
uncertain use through loaders, publication boundaries, compatibility policy,
history, or downstream evidence within scope. If external use cannot be bounded,
state that limit rather than calling the surface dead. A document or test may
describe a real requirement; artifacts referring to one another are not independent
proof of one. Age alone does not establish obsolescence.

Use [defensive-audit.md](defensive-audit.md) for a disputed mechanism and
[boundaries-and-lifecycle.md](boundaries-and-lifecycle.md) when ownership,
concurrent transitions, cancellation, or disposal determines whether a cut is safe.

## Choose justified cuts

For each meaningful candidate, explain the obligation and burden removed,
consumers and guarantees, affected artifacts, preserved behavior, any capability
surrendered, supporting evidence, and remaining uncertainty. A small cut needs a
short explanation; broad work may need a durable record for continuation. Do not
invent a score or optimize for finding counts and deleted lines.

Weigh confidence separately from benefit, consequence, reversibility, and effort.
Similarity is not redundancy: implementations may protect different owners or
failure windows. Prove candidate-exclusive members within shared files instead
of deleting mixed artifacts wholesale. Retain useful rejected findings and name
the missing fact for unresolved ones.

For replacement dependencies, compare exact semantics, residual local policy,
platform support, maintenance, footprint, and migration cost. Reuse reduces burden
only when it removes responsibility rather than moving it into a wrapper.

Contract and consumer analysis come first. Use
[ablation](defensive-audit.md#6-use-ablation-when-it-can-resolve-uncertainty) only
when a bounded experiment can resolve material uncertainty. Green checks establish
only exercised cases, not absence of unexercised or external consumers.

## Implement and verify

Group changes by the behavior or ownership boundary they retire. Follow affected
declarations through registration, dispatch, implementation, state, imports,
exports, generated inventories, callers, examples, documentation, and tests.
Preserve unrelated work and unique behavior checks; remove artifacts that only
pin intentionally retired behavior. Finish all authorized boundaries.

Remove compatibility glue only when its obligation is absent or retirement is
authorized with a suitable transition. Redirect consumers to the surviving state
owner instead of adding a synchronization layer. If workers are already involved,
give shared artifacts one owner and check interactions in the combined result;
this workflow does not require delegation.

Choose checks that could expose a real regression: residue searches, a compiler,
existing tests, a build, protocol comparison, or a real workflow as appropriate.
Capture a baseline when it helps distinguish prior failure from regression. Honor
the user's verification constraints; do not run every category mechanically.
Controlled measurements are needed for performance improvement claims.

When a check fails, determine whether the premise was wrong, the edit regressed
behavior, or the failure predates it. Repair or undo the affected cut; do not weaken
assertions, extend timeouts, or retry blindly to make it pass. A failing baseline
limits the evidence without blocking unrelated supported changes. Repeat passing
checks only for new changes, failures, or unresolved concerns.

For source-only changes, the diff can provide recovery. Data, configuration,
deployment, and publication need recovery appropriate to their side effects and
authority for those actions. Cleanup permission does not authorize publication.

## When consolidating design records

Establish the current owner from implementation, newer decisions, compatibility
policy, and inbound links. Classify records by which contracts and useful rationale
survive, not their titles or dates. Preserve immutable historical records; update
current owners and links instead of rewriting frozen history.

Before retiring a displaced record, carry forward unique rationale, alternatives,
consequences, useful warnings, known gaps, and conditions that could change the
decision. Repair links, indexes, paired translations, and metadata. Keep distinct
records where merging would hide a live contract or meaningful historical decision.
Use existing documentation conventions; a small item need not become a new ADR.

## When importing findings

Use the source's merge base or recorded filesystem basis to distinguish its own
contribution from inherited changes. Check whether its symbols, consumers, dynamic
paths, and compatibility obligations still match the destination. Treat stale
conclusions as hypotheses without repeating an entire audit unnecessarily.

Consolidate overlaps under the current owner; retain counterarguments and uncertainty.
Account for requested findings as retained, consolidated, rejected, superseded, or
unresolved. Distinguish proposals, applied changes, and exercised behavior. Apply
authorized changes according to current contracts and check interactions introduced
by combining them. Isolated success does not prove the combined result; unchanged
independent evidence need not be rerun without reason. External review updates
require authority beyond permission to inspect their findings.

## Deliver the result

Survey ends with coverage, supported findings, important retained candidates, and
unresolved questions. Change ends with the completed cuts, affected artifacts,
preserved or intentionally changed behavior, checks and baseline failures, limits,
and recovery for material side effects. A diff and concise response can carry this
record; no separate receipt file is required.

Distinguish source inspection, automated checks, build success, and real operation.
Report concrete blockers without presenting a partial slice as completion. Finding
no justified deletion is valid. After interruption, restore completed work and
pending decisions; reinvestigate only missing or invalidated evidence.
