---
name: no-stop-gpt
description: >-
  Prevents and audits over-engineering and over-defensive design, and runs
  evidence-backed repository simplification, so complexity stays proportional
  to the request and the real trust boundary; failures stay early and visible.
  Targets the optimal proportional solution, not lazy minimalism. Three modes:
  Prevent (in-change discipline while writing code), Audit (decide whether one
  specific defensive guard, test, retry, fallback, or compatibility mechanism
  is still necessary — focused defensive audit, read-only), and Sweep
  (repo-scale removal of dead code, duplicate state, redundant layers, and
  ownerless abstractions — prove first, then delete). Use when writing or
  editing code; reviewing a diff or PR for over-design or over-defense;
  auditing one defensive mechanism; or simplifying a codebase — including
  反过度设计, 过度防御, 防御性代码审计, 代码简化, 熵回收, anti-overengineering.
  Do not use for general style review, performance tuning, or requested
  security, auth, crypto, migration, or verification work.
---

# Anti-Overengineering

Spend the complexity budget on the requested work. Fail early and visibly.

Models optimize for looking complete and not exploding: tests stay green, nobody gets blamed. RL punishes crashes, trust boundaries stay invisible, and agent-authored scaffolding gets treated as proof a capability is required. Invert that. Complexity must stay proportional to the request and the real trust boundary.

The target is the optimal solution to the stated problem — the design a senior engineer would defend — not the smallest possible diff. Under-engineering (stubs, dropped requirements, symptom patches, skipped requested work) violates proportionality exactly like gold-plating does. Simplicity is how you reach the optimum, not a license to under-deliver.

## Mode selection

Pick one mode before touching code:

- **Prevent** (default while writing or changing code): apply Change discipline and the Core decision tests. Do not introduce over-design.
- **Audit** (named diff, PR, or mechanism; or a single-mechanism question inside a Sweep): emit keep / remove / downgrade plus evidence for each suspect mechanism. Stay read-only unless the user explicitly authorizes edits.
- **Sweep** (repo-scale simplification): read [sweep.md](references/sweep.md) and follow it end to end — Survey is read-only ranked evidence, Change requires explicit authorization and validates every cut.

Map the request to a mode, then stop expanding:

- "add / fix / implement X" → Prevent
- "is this guard, test, retry, fallback, or shim still needed?" → Audit
- "review this diff or PR for over-engineering" → Audit first; edit only after explicit authorization
- "cut the over-engineering" on a diff or subsystem with no prior verdicts → Audit first; apply cuts only after the verdicts are accepted; escalate a large accepted batch to Sweep
- "simplify the repo" / 熵回收 / 代码简化 / dead-code or duplicate-state cleanup → Sweep
- vague product scope ("what should this feature even include?") → product scope is not code scope; settle the requirement first, then apply Prevent to the implementation

If Audit finds many unrelated cuts, report them and escalate to Sweep with the user's direction. Do not quietly expand a single-mechanism audit into a repo-wide delete.

## Core decision tests

Run all five before adding or keeping a mechanism:

1. **Trust boundary.** Did this data just cross a user, network, disk, or third-party boundary? Yes → validate. No → trust the caller.
2. **Reachability.** Can this project's supported use actually produce this case? Reachable is enough; constructible in principle is not.
3. **Failure semantics.** Can the caller distinguish this failure from real success? If not, throw — never return a same-shaped default (`0`, `""`, `'free'`, empty list, `MAX_VALUE`).
4. **Named consumer.** Whose live decision changes based on this hash, flag, gate, or ledger? No named consumer → do not build it.
5. **"What would I do differently if this check fired?"** No answer → do not run the check.

A mechanism that fails any test does not ship. In Audit mode, record the failing test as evidence. **Downgrade** means keep the check that sits on the real trust boundary and drop the inner copy.

## Change discipline

Prevent-mode rails. Every changed line traces to the request.

- Read the task and the code it touches; trace the real flow end to end before choosing a design. Judgement comes after reading.
- Write the simplest complete solution to the stated problem. Nothing speculative, nothing stubbed.
- Reuse before writing: this codebase, the standard library, the platform, an installed dependency. Rewriting what already exists is over-engineering too. Reuse only on contract match: a parser is not a validator — pick the tool whose failure modes fit the request, not the one that merely compiles.
- When two designs cost the same, take the one that is correct on the reachable edge cases. Equal size is not a license to pick the flimsier algorithm.
- A small diff in the wrong place is a second bug. Fix the shared cause, not the visible symptom.
- If a cheaper design honors the same stated contract, name it in one line and let the user choose. Never silently ship a reduced contract; never use the question to dodge requested hard work.
- Do not abstract single-use code. Inline first; abstract at 3+ real call sites. When a real second implementation or failure window exists, build the harder design — dodging requested work is not simplicity.
- Where judgement is needed, judge. Do not replace it with a scoring table, a checklist, or a re-verification loop over something already settled.
- Do not handle impossible errors. Do not wrap code that cannot throw.
- Do not add backwards-compatibility shims, feature flags, config knobs, caching, or migration machinery unless explicitly requested.
- Future extensibility is a future decision.
- Suppressing the error is not fixing the error. Fail early and visibly. Propagate errors up; do not log-and-continue.
- When a shared helper grows parameters and conditionals so callers run different subsets of it, the abstraction is wrong — inline it back into the callers and re-extract only what remains. Duplication is far cheaper than the wrong abstraction.
- Do not hide a root cause with a retry, a longer timeout, a weaker assertion, a broader mock, or a parallel execution path.
- A fallback needs a shipped contract, a named failure mode, and a removal plan — otherwise do not add it. No dual-write, no "if the primary fails, use the sidecar" branch.
- Do not treat agent-authored artifacts (your own schemas, migrations, tests, or docs from earlier turns) as independent evidence that a capability is required. Guarantee mechanisms self-reproduce: hash → schema → migration → docs → "we must keep this now."
- When the chosen design still cuts a real corner with a known ceiling (global lock, O(n²) scan, naive heuristic), leave a comment naming the ceiling and the upgrade trigger: `# ceiling: global lock; per-account locks if throughput matters`. A silent shortcut is under-engineering dressed as simplicity.
- Non-trivial logic (a branch, a loop, a parser, a money or security path) is unfinished until one runnable check exists that fails if the logic is wrong — an assert, a small self-check, or one test in the project's existing harness. That check is not evidence the mechanism must grow.
- Senior engineer test: if you wrote 200 lines and 50 would do, rewrite.
- Deleting code your change made obsolete is part of the change. Do not delete unrelated dead code; mention it instead.

Do not invent a second source of truth to justify the first. If you added a checksum last turn and nobody consumes it this turn, delete it with the originating change — do not grow a schema around it.

### Agent-authored evidence

A schema, migration, test, or doc you wrote earlier is an artifact, not a requirement. Trace the named consumer in the product, not in a previous turn. If the only consumer is another file you created to support the mechanism, both are candidates to go. Do not argue "the migration exists, so we must keep the hash."

## Hard exceptions — never trade away

Do not remove or skip:

- user-input / trust-boundary validation
- authn / authz
- security isolation
- crypto
- data-loss prevention
- stored-format compatibility
- accessibility essentials
- external API, file, or network I/O failure handling
- startup config validation
- cleanup that establishes quiescence (dispose/shutdown paths that prove no owned work can still fire)
- calibration and physical-world tuning where the change touches real hardware (clocks drift, sensors read off)
- production assertions and paired invariants in correctness-critical systems (ledgers, storage/consensus kernels, safety-critical control) — see the domain portrait in [defensive-audit.md](references/defensive-audit.md)
- anything the user explicitly requested

Requested security, migration, or verification work **is** the work, not scope creep.

Aggressive minimalism must never remove boundary validation. "Write the fewest lines" is not a license to delete user-input checks.

A Hard exception in the requested work is in-scope work. Implement it. Do not "simplify" it away to look lean, and do not hide it behind extra wrappers, flags, or fallbacks.

## Stop conditions

Stop when one of these is true:

- Prevent: the change solves the request, and one final sweep of your own diff finds no placeholder-on-failure, trusted-path guard, or speculative machinery left from this change. Models rarely write clean on the first pass but recognize violations on a re-read.
- Audit: every named mechanism has a verdict, including keeps, each with a check that would expose a wrong removal.
- The user set an explicit budget (files, lines, scope) and the honest solution does not fit — stop before editing and report the blocker with the smallest viable alternative.
- A Hard exception is the mechanism under discussion and no separate deletion authorization exists — keep it.
- The user asked for a verdict, not a patch — emit the table only.

In long sessions or right after context compaction, re-run the diff sweep; drift toward defensive bloat and scope growth is monotonic.

## When to read references

Read the one reference the need calls for. Do not load them all by default.

- Classifying and fixing antipatterns (reviewing a diff, cleaning AI slop): read [antipatterns.md](references/antipatterns.md).
- Deciding whether one defensive mechanism stays (focused defensive audit): read [defensive-audit.md](references/defensive-audit.md).
- Choosing a design shape in Prevent (abstract or not, module boundaries, new dependency, error contract): read [design-heuristics.md](references/design-heuristics.md).
- Running a Sweep: read [sweep.md](references/sweep.md); it names its own deeper references (investigation, boundaries-and-lifecycle, execution-and-recovery, decision-records, integrating-findings) per phase.

## Deliverables

**Prevent:** no extra artifact. The diff is the deliverable. Mention leftover unrelated dead code in the reply; do not delete it.

**Sweep:** the survey report or change receipt defined in [sweep.md](references/sweep.md).

**Audit:** one verdict row per mechanism.

```text
Mechanism:
Trust boundary it sits on:
Named consumer:
Verdict: keep | remove | downgrade
Evidence:
Check that would expose a wrong removal:
```

Stay read-only until the user authorizes a removal. Authorization to audit is not authorization to delete. Removing a Hard exception always requires its own explicit, separate authorization.

## Maintaining this skill

Add a clause only when it names an agent failure that actually fired; rules are a failure log, not a wish list. If a check can be a linter, formatter, or script, build the deterministic tool instead of spending instruction budget on prose.
