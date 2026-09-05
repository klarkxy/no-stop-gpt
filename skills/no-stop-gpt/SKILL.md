---
name: no-stop-gpt
description: >-
  Keep code complexity proportional to requirements. Use during substantive code
  changes, reviews for overengineering or unnecessary defense, and codebase
  simplification. Do not trigger for pure explanation, formatting, or unrelated
  prose edits, or turn a specialist security or migration task into a cleanup.
license: SATA 2.0 (modified)
---

# No, Stop! GPT!

Deliver the complete requested behavior with the least unnecessary complexity.
Extra layers, duplicate state, speculative flexibility, and hidden failures make
changes harder to maintain. Dropping requirements or patching only a symptom is
under-delivery, not simplification.

## Mode and authority

- **Prevent:** lightweight guidance during substantive code changes. Ordinary
  edits need only the common principles below, with no separate report.
- **Audit:** answer a question about a mechanism, diff, or PR. Read-only requests
  produce conclusions and evidence. Read [defensive-audit.md](references/defensive-audit.md)
  for a focused mechanism decision; use [antipatterns.md](references/antipatterns.md)
  when concrete examples would help a diff review.
- **Sweep:** simplify a subsystem or repository. Read [sweep.md](references/sweep.md).
  Survey requests are read-only. Explicit requests to simplify, remove,
  consolidate, or apply findings authorize investigation and changes in that scope.

Authority follows the user's request, not the mode name. Authorized cleanup does
not need a second approval of intermediate findings. Switching modes or moving
to the next authorized boundary does not reset authorization. A review request
alone does not authorize edits to the user's working tree, even for an experiment.

Choose routine reversible implementation details. Ask when an unresolved choice
changes requirements, supported compatibility, authority, or irreversible effects.
Continue independent authorized work while that choice is open. Do not expand a
focused request into unrelated cleanup.

## Common decision principles

Use the relevant dimensions as reasoning aids, not five gates every line must pass.

1. **Boundary and ownership:** which guarantees hold here? Consider input trust,
   mutation rights, lifetime, concurrency, and the unit that owns failure.
   Same-process calls are not automatically trusted or immutable.
2. **Reachability:** can supported use produce the case? Include dynamic,
   external, persisted, and concurrent paths when relevant. A constructible test
   input alone does not establish a production requirement.
3. **Failure semantics:** can the caller distinguish failure from legitimate
   success or absence? Do not invent a permissive default to hide an error.
   Propagate or translate at the responsible boundary; fail a request or job
   without aborting a healthy process. Recover where state is known good.
4. **Consumer and purpose:** which behavior, guarantee, or operational decision
   needs the mechanism? Missing evidence makes it an investigation candidate,
   not an instruction to delete it immediately.
5. **Decision value:** what would a check's result change? Choose evidence that
   answers an outstanding question; avoid repeating settled checks.

Compare designs that satisfy the same contract. Prefer fewer concepts and
coordination obligations, not fewer lines at the expense of clarity. Reuse code,
platform facilities, and dependencies when their semantics fit. An abstraction
can serve a meaningful boundary with one caller; repeated syntax alone does not
justify sharing different behavior.

Read enough of the affected flow to understand the change and its consequences.
Investigate more deeply when ownership, compatibility, or failure behavior is
unclear. Fix the shared cause and remove residue created by the change; leave
unrelated cleanup outside the assignment.

Add guards, retries, caches, flags, and layers for current requirements or supported
failure modes. Do not use them to mask a root cause. Explain material tradeoffs
or hidden ceilings where useful, without mandatory comments for routine choices.
Read [design-heuristics.md](references/design-heuristics.md) only when a design
question needs more detail.

## Evidence and necessary protections

An agent-authored schema, test, migration, or document is an artifact, not
independent proof of a requirement. Trace the contract and actual consumers;
do not dismiss a real requirement merely because an agent implemented it.

Use contract and consumer evidence first. If a material uncertainty is suitable
for an experiment, consider [ablation](references/defensive-audit.md#6-use-ablation-when-it-can-resolve-uncertainty).
A green check supports only its covered cases. It does not prove that no consumer
exists or that deletion is safe. Retain or mark unresolved when missing evidence matters.

Preserve required input validation, authentication and authorization, security
isolation, cryptography, data integrity and loss prevention, stored-format
compatibility, accessibility, I/O failure handling, startup validation, and cleanup
guarantees. Physical-world and correctness-critical systems may need fault
detection and invariants beyond ordinary application assumptions.

These are protected outcomes, not exemptions for every existing implementation.
Remove a duplicate only when the surviving owner preserves the same guarantee
and failure window. General cleanup does not authorize withdrawing a protection
or supported capability. Requested security, migration, or verification is work
to complete, not scope creep.

## Complete the request

- **Prevent:** complete the implementation and applicable checks. Inspect changed
  behavior for speculative machinery or hidden failures; do not require ablation
  for every guard, parameter, or layer.
- **Audit:** answer the named questions with evidence and material uncertainty.
  Use keep / remove / downgrade / decide. Simple questions need short answers;
  multiple or consequential findings need structured records.
- **Sweep Change:** finish all authorized boundaries, including affected callers
  and supporting artifacts. Report concrete blockers and evidence limits rather
  than stopping after the first implementation or successful cut.

Reuse checks that can expose a relevant regression. Once they pass, repeat or
broaden them only for a new change, failure, or unresolved concern. Honor explicit
verification constraints and state what was not checked. A supported conclusion
that nothing should be removed is a valid result.

After interruption or context compaction, restore the objective, accepted
decisions, completed work, and remaining items. Revisit missing or invalidated
evidence; compaction alone does not require another full review.

## Maintain the skill

Add guidance for concrete recurring failures not covered by existing principles.
Keep examples conditional, avoid model-specific assumptions, and give each rule
one authoritative home. Merge repetitions, downgrade overly broad rules, and
remove instructions whose motivating conditions no longer hold. Use a script
only when repeated deterministic work justifies maintaining it.
