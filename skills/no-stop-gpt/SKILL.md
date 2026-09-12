---
name: no-stop-gpt
description: >-
  Review overengineering and simplify code. Use for complexity reviews, scoped
  cleanup, or implementation choices about added layers, state, or defenses.
license: SATA 2.0 (modified)
metadata:
  version: "0.1.7"
---

# No, Stop! GPT!

Deliver the complete requested behavior with the fewest unnecessary concepts and
coordination obligations. Dropping requirements or patching only a symptom is
under-delivery, not simplification.

## Use only the guidance the task needs

- **Prevent:** apply the principles below while implementing the request. Read
  [design-heuristics.md](references/design-heuristics.md) if an abstraction, reuse,
  or error-contract tradeoff needs more detail. No separate audit report is needed.
- **Audit:** answer the named complexity question with evidence. Use
  [defensive-audit.md](references/defensive-audit.md) for a disputed mechanism or
  [antipatterns.md](references/antipatterns.md) for diff scope and review examples.
- **Sweep:** use [sweep.md](references/sweep.md) for subsystem or repository cleanup.
- For cuts involving ownership, concurrent transitions, cancellation, or disposal,
  consult [boundaries-and-lifecycle.md](references/boundaries-and-lifecycle.md).

These are reference routes, not stages to complete in sequence. Read only what
can resolve a question in the current task; a local edit needs no repository map.
Do not turn specialist security or migration work into a general cleanup.

## Scope and completion

Use the request to establish the observable result: an evidence-backed answer for
a review, or working behavior with affected callers and supporting artifacts
updated for an implementation. Infer routine reversible details without a separate
planning or approval step.

A review or survey is read-only. Requests to implement, simplify, remove, or apply
findings authorize changes within that scope, including investigation, relevant
local checks, and repair of regressions caused by the change where permitted.
Continue through that result; a first patch, intermediate finding, or successful
cut is not a handoff point. Do not ask again for already authorized work.

Pause only the dependent action when a material question about requirements,
compatibility, authority, or irreversible effects needs the user's decision.
Continue independent authorized work. Cleanup alone does not authorize retiring
supported capabilities, publication, deployment, or unrelated changes.

## Complexity decisions

- Compare designs that satisfy the same contract. Prefer readable control flow
  and fewer responsibilities over fewer lines. Follow project conventions; reuse
  existing facilities when their semantics fit. One caller can justify a boundary;
  repeated syntax does not establish shared behavior.
- Judge guards, retries, caches, flags, and layers by a current consumer, guarantee,
  or supported failure mode. Trace the affected flow when the purpose is unclear.
  Missing evidence makes a mechanism an investigation candidate, not dead code.
- Compare the owner and failure window before removing duplicate checks or state.
  Same-process data is not automatically trusted or immutable. A constructible
  test input alone does not establish a supported production case.
- Preserve the caller's distinction between failure, valid absence, and success.
  Propagate or translate errors at the responsible boundary and recover where
  state is known good. Do not hide a root cause behind a permissive default or
  abort a healthy process for a request-scoped failure.

## Evidence and protections

Tests, schemas, and documents can describe a contract; their existence alone is
not independent proof that a mechanism is required. Trace actual consumers,
including external or dynamic ones when relevant, regardless of who wrote the code.
Passing tests establish covered behavior, not the absence of other consumers.
Retain a mechanism or mark it unresolved when a material evidence gap remains.

Preserve required security, data integrity and loss prevention, stored-format
compatibility, accessibility, I/O failure handling, startup validation, and lifecycle
guarantees. Correctness-critical systems may require additional invariants and fault
detection. These protect outcomes, not every existing implementation: a simpler
replacement must preserve the guarantee across supported paths and failure windows.

Choose verification that can change the decision or expose a relevant regression.
Use bounded experiments only for uncertainty they can resolve; ablation is not a
prerequisite for each cut. Once applicable checks pass, repeat or broaden them only
for a new change, failure, unresolved concern, or explicit verification requirement.
Honor the user's verification constraints and distinguish performed checks from proposals.

Deliver the answer or completed change with material evidence, limitations, and
blockers. Finding nothing justified to remove is a valid result. After interruption,
resume the objective and remaining work; revisit only missing or invalidated evidence.

## Maintain the skill

Keep instructions that change decisions for this workflow. Revisit old workarounds
as models and tools improve; remove obsolete scaffolding and repeated generic advice.
Keep discovery concise, route conditional detail to its existing reference, and
preserve real constraints for all supported agents without a model-specific recipe.
