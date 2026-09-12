# Focused defensive audit

Use for a guard, test, retry, fallback, copy, rollback, or compatibility mechanism
whose necessity is in question. Follow [SKILL.md](../SKILL.md) for authority and
protected outcomes. Read-only questions end in verdicts; authorized cleanup may
continue into changes without another approval stage.

Use the sections that can change the verdict; they are not a required sequence.

## Locate the boundary

Trace the value's origin, owners, mutation rights, lifetime, and failure domain
as needed to understand the guarantee. Focus on dimensions that could change
the verdict rather than filling a checklist for its own sake.

## Borrowed handoffs and owned boundaries

A **borrowed handoff** relies on guarantees already established and maintained
by the caller or component. Private same-process calls can qualify only when
types, ownership, and lifetime support that conclusion.

An **owned boundary** establishes a guarantee for incoming data or work: input,
configuration, tool output, storage, queues, protocols, plugins, workers, or
asynchronous lifetimes. One process can contain several boundaries.

## Identify unnecessary duplication

Re-validating unchanged data, copying an object whose mutation is controlled,
or catching errors a helper cannot produce can be redundant. Verify the premise:
earlier validation does not protect against later mutation, other callers, or
new failure windows.

## Compare guarantees and failure windows

Identify the owner, transition, and failure window each mechanism protects.
HTTP validation and worker queue validation can both be necessary. An inner
duplicate is removable only if the surviving owner covers all supported paths.

For cancellation, publication, rollback, or disposal, consult
[boundaries-and-lifecycle.md](boundaries-and-lifecycle.md) when those guarantees
are material to the decision.

## Establish contract and consumer evidence

Follow producers, consumers, failures, and the decision the mechanism changes.
Keywords such as `hash`, `lock`, or `fallback` identify leads, not defects. Include
dynamic and external consumers when the surface supports them.

Tests and documents can explain requirements, but their existence alone does not
prove one. An agent-created artifact supporting another agent-created mechanism
is not independent product evidence. Useful behavior checks still matter regardless
of who wrote them.

If evidence settles the question, report it. If a missing fact changes whether
removal is safe, retain the mechanism pending that fact or use `decide`.

## Use ablation when it can resolve uncertainty

An ablation removes or disables a mechanism in a bounded experiment. Use it when
contract and consumer analysis leave a material question the experiment can answer,
not as a requirement for every verdict or change.

- Isolate the changed mechanism and keep workload and environment comparable to
  the control. Identify the behavior that could expose a wrong cut.
- Establish that the probe reaches the relevant path and detects the lost guarantee.
  A green suite that misses the path says nothing about that mechanism.
- Use an authorized isolated copy or test environment for read-only audits.
  Switching branches in the user's working tree does not make edits read-only.
  If isolation or authority is unavailable, report the evidence gap.
- Reuse a probe where possible; add one only when permitted and useful. Honor
  user constraints excluding verification rather than inventing experiments.
- Report uncertainty from flaky or uncontrolled observations. Investigate ordering
  when feasible; do not repeat until a desired result appears. Reproducible manual
  observations can be evidence when their limits are stated.
- A regression supports the need for a guarantee, though its implementation may
  still be replaceable. No observed change supports only the exercised cases,
  not absence of consumers or universal safety of deletion.
- Happy-path success cannot establish that authorization, input validation,
  corruption detection, or cleanup is unnecessary. Exercise relevant failure cases
  when needed and preserve required outcomes regardless of representation.

Record what changed, what was observed, and what remains untested. Stop when the
experiment answers the question or cannot produce a useful signal.

## State the verdict

- **keep:** a supported behavior needs the mechanism, or available evidence does
  not justify removal. Distinguish those reasons.
- **remove:** evidence supports removal while preserving required outcomes.
  Removing an error-swallowing catch may need propagation or one boundary
  translation; failure reporting must survive.
- **downgrade:** retain the guarantee with less machinery, such as one validation
  instead of inner copies, or request failure instead of aborting a healthy process.
- **decide:** unresolved evidence or a material contract conflict prevents a justified
  choice. State the missing fact or alternatives and consequences. Resolve routine
  ambiguity within existing authority; ask the user only for material choices.

A simple question needs a concise verdict, evidence, and meaningful limitation.
For multiple or consequential findings, use a record such as:

```text
Mechanism and scope:
Guarantee, owner, and failure unit:
Consumer and reachable cases:
Verdict: keep | remove | downgrade | decide
Evidence and uncertainty:
Relevant check or trace, with result if performed:
Consequence and authorization needed, if any:
```

Separate suggested checks from performed checks. Do not fabricate results to fill fields.

## Correctness-critical domains

Ledgers, storage and consensus kernels, physical control, and other systems can
need production assertions, paired invariants, bounded execution, and fault
detection because corruption costs more than lost availability. Establish the
actual failure model: bitrot, replica divergence, and hardware drift can justify
mechanisms unnecessary in routine application paths.

Preserve these guarantees. A request-scoped error alone does not justify process
termination, and a safety-critical label does not justify unrelated layers.
