# Sweep: subsystem and repository simplification

Reduce unnecessary concepts and coordination obligations while preserving the
requested behavior. Finding no justified deletion is a valid outcome. Follow
[SKILL.md](../SKILL.md) for authority, common principles, and protected outcomes.

## Select scope and outcome

- **Survey:** a request to audit, investigate, or propose simplification. Stay
  read-only and return evidence-backed findings.
- **Change:** an explicit request to simplify, remove, consolidate, refactor, or
  apply findings. Investigate and implement within that authorization without
  waiting for separate approval of intermediate findings.
- **Focused:** cover the named subsystem or mechanism and its affected consumers.
- **Broad:** account for the requested repository domains, including blind spots;
  do not stop at the first easy candidate.

Mode changes do not reset permissions. Retiring a supported capability, weakening
protection, or surrendering compatibility is a material choice unless already
chosen by the user. Resolve it while continuing independent authorized work.

## Establish the affected contract

Read applicable repository instructions and inspect working-tree state before
editing. Consult architecture, manifests, test guidance, generated-file conventions,
and decision records where they affect the candidate; do not require a full
repository map for a small cleanup.

Trace the relevant entrypoints, consumers, state, and failure behavior. Include
dynamic loading, public interfaces, persisted data, and compatibility when present.
Distinguish user changes and generated or externally maintained surfaces.

For broad work, use [investigation.md](investigation.md) to organize coverage.
For focused work, consult it only when discovery, external consumers, or dependency
substitution needs more guidance. Use [boundaries-and-lifecycle.md](boundaries-and-lifecycle.md)
when ownership, concurrent transitions, or disposal determines whether a cut is safe.
A named mechanism can use [defensive-audit.md](defensive-audit.md) without pausing the sweep.

## Select supported cuts

For a meaningful candidate, capture enough evidence to explain:

- the obligation and maintenance burden removed;
- consumers and required guarantees, including uncertain or external ones;
- the declarations, implementations, callers, and artifacts affected;
- preserved behavior and any capability or compatibility surrendered;
- evidence supporting the choice, relevant checks, and remaining uncertainty.

A short explanation is sufficient for a small local cut. Broad or consequential
work benefits from a durable record so it can be continued without rediscovery.
Do not create ledgers or numerical scoring systems for ordinary edits.

Contract and consumer analysis come first. Use ablation only when a suitable
experiment could resolve remaining uncertainty. Green checks alone do not prove
absence of consumers. Keep or mark unresolved when the missing evidence matters.

Compare benefit with confidence, consequence, and effort. Moving complexity into
a replacement wrapper is not necessarily an improvement. Prove candidate-exclusive
members inside shared files rather than deleting an entire mixed artifact.

## Act and finish

Survey ends with findings, important retained candidates, and unresolved questions.
Change continues through all authorized boundaries using
[execution-and-recovery.md](execution-and-recovery.md). One reviewable boundary is
a useful batch size, not a limit on completion.

When requested documentation consolidation or a cut affects a design record,
use [decision-records.md](decision-records.md). When importing findings from another
branch or task, use [integrating-findings.md](integrating-findings.md). Neither is
a prerequisite for an ordinary local cleanup.

Report what changed or was retained, the evidence reached, and concrete blockers.
Distinguish source conclusions from checks and real runtime observations. Do not
stop after an initial implementation when requested work remains.
