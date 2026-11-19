# Sweep: subsystem and repository simplification

Reduce unnecessary concepts and coordination obligations while preserving the
requested behavior. [SKILL.md](../SKILL.md) owns common principles, authority,
and protected outcomes. Use the relevant sections for investigation and delivery;
they do not require a separate artifact or approval at each step.

Do not spend a sweep on code that already serves its contract clearly or on
cosmetic work in modules about to be replaced. For old paths whose dependencies
are unclear, use a reversible cut to learn what must move or survive. Follow
[legacy-cleanup.md](legacy-cleanup.md) for deletion recipes and repair decisions.

## Scope and outcome

- **Survey:** audit, investigate, or propose simplification. Stay read-only and
  return evidence-backed findings; do not create repository files unless requested.
- **Change:** an explicit request to simplify, remove, consolidate, or apply
  findings authorizes investigation and changes within that scope.
- **Focused:** cover the named boundary and its affected consumers.
- **Broad:** cover the requested repository domains, including blind spots;
  do not stop at the first easy candidate.

## Investigate the contract

Read applicable instructions and working-tree state. Consult manifests, test and
generation conventions, architecture, or history where they affect the change.
Distinguish user changes, generated files, and externally maintained surfaces.

Consult blame and commit context when a mechanism's purpose or external
obligations are unclear: a performance need, platform constraint, or past incident
may explain what survives. Recorded reasons can be stale, and missing history is a
lead to investigate or test—not a blanket ban on a reversible local cut.

For a diff-scoped request, follow the
[diff scope guidance](antipatterns.md#establish-the-diff-scope).

For broad work, partition by responsibility: entrypoints and public interfaces,
configuration, domain state, persistence, protocols and dynamic loading, background
work and resource ownership, packages, tests, and operational documentation.
Record the relevant paths examined and meaningful blind spots. Start cutting a
useful boundary without waiting for a complete inventory of every candidate.

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
state that limit rather than calling the surface dead. Artifacts referring to one
another are not independent proof of a requirement, and age alone does not
establish obsolescence.

Use [defensive-audit.md](defensive-audit.md) for a disputed mechanism and
[boundaries-and-lifecycle.md](boundaries-and-lifecycle.md) when ownership,
concurrent transitions, cancellation, or disposal determines whether a cut is safe.

## Choose justified cuts

For a meaningful candidate, explain why the change removes burden while preserving
the contract. Include affected consumers, evidence, and uncertainty when they matter
to the decision. A small cut needs a short explanation; broad work may benefit from
a durable record. Do not optimize for finding counts or deleted lines.

Weigh confidence separately from benefit, consequence, reversibility, and effort.
Similarity is not redundancy: implementations may protect different owners or
failure windows. Prove candidate-exclusive members within shared files instead
of deleting mixed artifacts wholesale. Retain useful rejected findings and name
the missing fact for unresolved ones.

For replacement dependencies, apply the fit and full ownership-cost comparison in
[design-heuristics.md](design-heuristics.md#dependencies-and-reuse). Reuse reduces
burden only when it removes responsibility rather than moving it into a wrapper.

Read enough to choose a useful cut, then try it. Compiler errors, test failures,
and exercised workflows can reveal the remaining dependencies faster than more
speculative analysis. Use
[ablation](defensive-audit.md#use-ablation-when-it-can-resolve-uncertainty) when a
disputed guarantee needs a controlled comparison; no separate experiment or report
is required for an ordinary delete-and-repair pass.

## Implement and verify

Group changes by the behavior or ownership boundary they retire. Follow affected
declarations through registration, dispatch, implementation, state, imports,
exports, generated inventories, callers, examples, documentation, and tests.
Preserve unrelated work and unique behavior checks; remove artifacts that only
pin intentionally retired behavior. Finish all authorized boundaries.

Choose sequencing for the actual dependencies and risks. These techniques are
options, not stages every replacement must pass:

- Use a preparatory refactor when tangled dependencies prevent a safe direct
  change; create only the seam the change needs.
- Use parallel change when consumers cannot migrate in one safe step. Introduce
  the replacement, migrate consumers, and retire the old path when its supported
  obligations end. Complete the authorized transition; retain a temporary path
  only for a concrete remaining obligation. A direct replacement can be preferable
  when all consumers can move together.
- Add characterization tests when behavior to preserve lacks useful coverage and
  the uncertainty matters to the cut. Reuse existing tests or other relevant
  checks when they already answer the question; do not freeze a bug the user
  requested fixing.
- When dependency order is unclear, make the cut and use the resulting failures
  as the repair queue. Move callers to the surviving path and remove newly dead
  support code. Undo only the affected cut if needed to resolve a prerequisite,
  then resume; do not use wholesale restoration as the default repair.

Remove compatibility glue only when its obligation is absent or retirement is
authorized with a suitable transition. Redirect consumers to the surviving state
owner instead of adding a synchronization layer. If workers are already involved,
give shared artifacts one owner and check interactions in the combined result;
this workflow does not require delegation.

Choose checks that could expose a real regression: residue searches, a compiler,
existing tests, a build, protocol comparison, or a real workflow as appropriate.
Capture a baseline when it helps distinguish prior failure from regression; do not
run every category mechanically. Controlled measurements are needed for performance
improvement claims.

Keep cleanup commits separate from feature and fix work so each stays reviewable
and revertible. When a cut spans hundreds of sites, prefer a codemod or scripted
transform over manual edits, and spot-check the transformed output.

Before deduplicating CI checks, confirm that the surviving run covers the target
commit and relevant platform, build configuration, and dependencies, and actually
runs on the required path. Identical commands in different jobs are not enough:
a Linux pass may leave macOS behavior untested, and an older pass does not cover
later edits. Reuse applicable evidence without turning deduplication into a gap.

For documentation-only edits, check changed facts, links, and paired translations
where maintained. Run documentation generation or rendering when affected; an
unrelated application build does not verify prose.

When a check fails, classify whether it exposes a remaining caller, an obsolete
implementation assertion, a lost required behavior, or a pre-existing failure (see
[legacy-cleanup.md](legacy-cleanup.md#repair-without-rebuilding-the-old-design)
for repair decisions). Repair in the surviving design, restoring only the
necessary piece if repair cannot preserve the contract; do not weaken behavior
checks, extend timeouts, or retry blindly to make it pass. A failing baseline
limits the evidence without blocking unrelated supported changes, and repair and
verification must finish before calling the cleanup complete.

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

If a finding's target no longer matches, recheck whether the issue survives;
a missing text anchor does not prove another edit fixed it. Reconcile conflicting
proposals against the contract before choosing a rewrite.

## Deliver the result

Survey ends with coverage, supported findings, important retained candidates, and
unresolved questions. Change ends with the completed cuts, affected artifacts,
preserved or intentionally changed behavior, checks and baseline failures, limits,
and recovery for material side effects. A diff and concise response can carry this
record; no separate receipt file is required.

Distinguish source inspection, automated checks, build success, and real operation.
Report concrete blockers without presenting a partial slice as completion.
