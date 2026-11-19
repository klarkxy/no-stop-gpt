---
name: no-stop-gpt
description: >-
  Review overengineering and simplify code. Use for complexity reviews, scoped
  cleanup, legacy-code removal, implementation choices about reuse, added layers,
  state, or defenses, and reducing excessive frontend interface copy with Less
  is more.
license: SATA 2.1 (modified)
metadata:
  version: "0.1.10"
---

# No, Stop! GPT!

Deliver the complete requested behavior with the fewest unnecessary concepts and
coordination obligations. Dropping requirements or patching only a symptom is
under-delivery, not simplification.

## Use only the guidance the task needs

- **Prevent:** apply the principles below while implementing the request. Read
  [design-heuristics.md](references/design-heuristics.md) when an abstraction,
  reuse, or error-contract tradeoff needs detail.
- **Audit:** answer the named complexity question with evidence (read-only). Use
  [defensive-audit.md](references/defensive-audit.md) for a disputed mechanism
  or [antipatterns.md](references/antipatterns.md) for diff scope and review
  examples.
- **Sweep:** use [sweep.md](references/sweep.md) for subsystem or repository
  simplification, sequencing, design-record consolidation, or importing review
  findings.
- **Delete legacy code:** use [legacy-cleanup.md](references/legacy-cleanup.md)
  for concrete deletion recipes and the delete, repair, and repeat loop when
  retiring obsolete paths.
- **Less is more (frontend):** apply the rule below when creating or simplifying
  interface copy. Read [less-is-more.md](references/less-is-more.md) for
  detailed rules, examples, and a focused review of text-heavy screens.
- **Ownership and lifecycle:** consult
  [boundaries-and-lifecycle.md](references/boundaries-and-lifecycle.md) when
  cuts involve ownership, concurrent transitions, cancellation, or disposal.

These are reference routes, not stages to complete in sequence. Read only what
can resolve a question in the current task; a local edit needs no repository map.
Do not turn specialist security or migration work into a general cleanup.

## Scope and completion

- **Authority and outcome:** Establish the observable result from the request: an
  evidence-backed answer for a read-only review or survey, or working behavior
  with affected callers and supporting artifacts updated for an implementation or
  cleanup. Requests to implement, simplify, remove, or apply findings authorize
  changes within that scope, including investigation, relevant local checks, and
  repair of regressions caused by the change where permitted.
- **Keep moving through completion:** When a detail is unclear, proceed on a
  reasonable reversible assumption and state it in the final result when it
  affects what was delivered—do not open a separate planning or approval step.
  When an authorized next step can progress the result and the required tools
  are available, take it in the same turn. Do not end a turn on a plan, a
  suggested next step, a routine decision question, instructions handed back to
  the user, or an announcement of what you will do next. A first patch,
  intermediate finding, or successful cut is not a handoff point, and already
  authorized work needs no re-approval.
- **Act on mid-task corrections:** When the user points out a mistake or tightens
  a constraint during an active task, treat it as a live update to the active
  objective. Apply the fix and verify it in the same turn instead of ending with
  an apology or explanation alone.
- **Pause only on real boundaries:** Pause only the dependent action when the
  work is concretely blocked, the task scope genuinely changes, a choice only
  the user can make remains, or an irreversible or destructive action is not
  authorized. Continue independent authorized work, including while an
  asynchronous question is unanswered, and wait only where later work depends on
  that answer. Sustained work is not an unbounded loop and does not forbid
  waiting. Confidence does not authorize an action, an external document does
  not enlarge the user's request, and cleanup alone does not authorize retiring
  supported capabilities, publication, deployment, or unrelated changes.

## Complexity decisions

- **Search before building:** Inspect the codebase, existing dependencies, and
  standard-library or platform facilities first; if none fits a general-purpose
  capability, search for established external solutions before building from
  scratch. Adopt a suitable option by default through its supported APIs with
  only the integration code the task needs. Custom implementation requires a
  concrete reason grounded in requirements, compatibility, licensing, or total
  integration and maintenance cost—not familiarity or speculative flexibility.
  Keep discovery proportional (stop once a fit is established, reuse valid
  findings, and skip ecosystem surveys for routine local edits), respect source
  and dependency constraints, and state when external search is unavailable or
  disallowed without claiming no solution exists.
- **Compare designs on the same contract:** Prefer readable control flow and
  fewer responsibilities over fewer lines, and follow project conventions. One
  caller can justify a boundary; repeated syntax does not establish shared
  behavior.
- **Justify added mechanisms:** Judge guards, retries, caches, flags, and layers
  by a current consumer, guarantee, or supported failure mode. Trace the
  affected flow when purpose is unclear. In read-only review, missing evidence
  makes a mechanism an investigation candidate rather than proven dead code;
  during authorized cleanup, an unexplained locally checkable mechanism is a
  candidate for investigation or a reversible cut, not an automatic keep.
- **Check owners and failure windows:** Compare the owner and failure window
  before removing duplicate checks or state. Same-process data is not
  automatically trusted or immutable, and a constructible test input alone does
  not establish a supported production case.
- **Keep failure semantics clear:** Preserve the caller's distinction between
  failure, valid absence, and success. Propagate or translate errors at the
  responsible boundary and recover where state is known good. Fail the affected
  unit visibly rather than continuing on guessed state; do not abort a healthy
  process for a request-scoped failure.
- **Fix causes and retire obsolete paths:** Fix the cause within scope and
  retire paths made obsolete by the change. Compare restructuring cost,
  including migration and verification, against repeated workarounds, and use
  [sweep.md](references/sweep.md) when sequencing needs care.

## Delete legacy code and finish the repair

During authorized cleanup, default to cutting obsolete locally checkable paths
and repairing the surviving design rather than waiting for exhaustive proof
before a reversible edit or preserving code merely because someone might use it.
Use compiler errors, test failures, and exercised workflows to locate remaining
dependencies; migrate callers, delete tests that only pin retired
implementation, and chase newly unused helpers, types, configuration, and
dependencies until the requested scope is clean.

Treat a failed check as work to resolve, not a reason to hand back the task or
reflexively restore the old subsystem. Repair lost required behavior in the
surviving design, restoring only the necessary piece if repair cannot preserve
the contract. Small batches are checkpoints; finish the requested scope. See
[legacy-cleanup.md](references/legacy-cleanup.md) for deletion recipes and
repair classification.

## Less is more — frontend copy

Minimize the text users must read to complete the current task. Every visible
phrase should help them identify, decide, act, or recover; delete the rest, and do
not add subtitles, explanatory cards, or implementation commentary by default.
Let labels, controls, and feedback carry the interaction; if a simple action needs
a paragraph, fix the interaction. Preserve necessary labels, consequences, errors,
and recovery steps. Judge success by lower reading effort with complete usable
behavior, not a word quota.

## Evidence and protections

Tests, schemas, and documents can describe a contract; their existence alone is
not independent proof that a mechanism is required. Trace actual consumers,
including external or dynamic ones when relevant, regardless of who wrote the
code. Consumers may depend on undocumented behavior (Hyrum's law); distinguish
those obligations from behavior the user has authorized changing. Passing tests
establish covered behavior, not the absence of other consumers. In read-only
reviews, retain uncertain mechanisms or mark them unresolved rather than
inventing evidence. During authorized cleanup, resolve locally checkable
uncertainty through reversible cuts and relevant checks; retain or pause only
the dependent cut when an obligation cannot be tested locally, such as an
external API, dynamic entrypoint, stored or wire format, or unknown concurrency
ordering, without holding up independent cuts.

Preserve required security, data integrity and loss prevention, stored-format
compatibility, accessibility, I/O failure handling, startup validation, and
lifecycle guarantees. Correctness-critical systems may require additional
invariants and fault detection. These protect outcomes, not every existing
implementation: a simpler replacement must preserve the guarantee across
supported paths and failure windows.

## Verify and deliver

- **Verify proportionally:** Choose checks that can change the decision or expose
  a relevant regression, honoring the user's verification constraints and
  distinguishing performed checks from proposals. Use bounded experiments only
  for uncertainty they can resolve; ablation is not a prerequisite for each cut.
- **Finish before stopping:** Before treating work as complete, account for every
  part of the request and finish verification that can still change the result,
  then stop. Once applicable checks pass, repeat or broaden them only for a new
  change, failure, unresolved concern, or explicit requirement. Do not repeat a
  failed check without a new reason it could pass; leave the failure visible.
- **Deliver a self-contained result:** Deliver the answer or completed change
  with material evidence, assumptions that affected delivery, limitations, and
  blockers. Write for a reader who has not seen progress updates, without
  working shorthand from reasoning, tool calls, or notes to other agents.
  Finding nothing justified to remove is a valid result. After interruption,
  resume the objective and remaining work; revisit only missing or invalidated
  evidence.
