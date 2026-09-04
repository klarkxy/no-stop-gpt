---
name: no-stop-gpt
description: >-
  Prevents and audits over-engineering and over-defensive design, and runs
  evidence-backed repository simplification, so complexity stays proportional
  to the request and the real trust boundary; failures stay early and visible.
  Targets the optimal proportional solution, not lazy minimalism. Three modes:
  Prevent (discipline while writing code), Audit (decide whether one
  defensive guard, test, retry, fallback, or compatibility mechanism is
  still necessary — focused defensive audit, read-only), and Sweep
  (repo-scale removal of dead code, duplicate state, redundant layers, and
  ownerless abstractions — prove first, then delete). Use when writing
  code; reviewing a diff or PR for over-design or over-defense;
  auditing one defensive mechanism; or simplifying a codebase — including
  反过度设计, 过度防御, 防御性代码审计, 代码简化, 熵回收, anti-overengineering,
  let it crash, 任其崩溃, Occam's razor, 奥卡姆剃刀, ablation, 消融试验.
  Do not use for general style review, performance tuning, or requested
  security, auth, crypto, migration, or verification work.
license: SATA 2.1
---

# Anti-Overengineering

Spend the complexity budget on the requested work. Fail early and visibly, at the unit that owns the work.

Models optimize for looking complete and not exploding: tests stay green, nobody gets blamed. RL punishes crashes, trust boundaries stay invisible, and agent-authored scaffolding gets treated as proof a capability is required. Invert that. Complexity must stay proportional to the request and the real trust boundary.

The target is the optimal solution to the stated problem — the design a senior engineer would defend — not the smallest possible diff. Under-engineering (stubs, dropped requirements, symptom patches, skipped requested work) violates proportionality exactly like gold-plating does. Simplicity is how you reach the optimum, not a license to under-deliver.

Two instruments run through every mode. **Occam's razor** chooses between designs: of the designs that honor the same stated contract, ship the one with the fewest entities — types, states, layers, flags, dependencies, assumptions. Count entities, not lines. The razor cuts entities, never requirements: a design that drops a stated requirement is not simpler, it is wrong. **Ablation** decides whether a mechanism that already exists is load-bearing: remove it, run the check that would fail if it mattered, look. No observable change under a check that can fail → no named consumer. Silence from a check that could never have failed is not evidence.

## Mode selection

Pick one mode before touching code:

- **Prevent** (default while writing or changing code): apply Change discipline and the Core decision tests. Do not introduce over-design.
- **Audit** (named diff, PR, or mechanism; or a single-mechanism question inside a Sweep): emit keep / remove / downgrade / decide plus evidence for each suspect mechanism. Stay read-only unless the user explicitly authorizes edits.
- **Sweep** (repo-scale simplification): read [sweep.md](references/sweep.md) and follow it end to end — Survey is read-only ranked evidence, Change requires explicit authorization and validates every cut.

Map the request to a mode, then stop expanding:

- "add / fix / implement X" → Prevent
- "is this guard, test, retry, fallback, or shim still needed?" → Audit
- "review this diff or PR for over-engineering" → Audit first; edit only after explicit authorization
- "全面审查过度防御" / audit a whole repo or several subsystems for over-defense with no named diff → Sweep in Survey mode, with the defensive audit applied per mechanism; the verdict rows are its proof records. Audit is for one diff, PR, or mechanism.
- "cut the over-engineering" on a diff or subsystem with no prior verdicts → Audit first; apply cuts only after the verdicts are accepted; when the accepted verdicts span more than one ownership boundary, switch to Sweep Change before the first edit — read [sweep.md](references/sweep.md) and its execution reference then, not after the tree is half-cut
- "simplify the repo" / 熵回收 / 代码简化 / dead-code or duplicate-state cleanup → Sweep
- vague product scope ("what should this feature even include?") → product scope is not code scope; settle the requirement first, then apply Prevent to the implementation

If Audit finds many unrelated cuts, report them and let the user decide whether to escalate to Sweep. Do not quietly expand a single-mechanism audit into a repo-wide delete. "你自行决定" / "use your judgement" on a multi-boundary verdict list is that escalation: it makes the batch a Sweep Change with proof records and per-boundary validation, not a faster Audit.

## Core decision tests

Run all five before adding or keeping a mechanism:

1. **Trust boundary.** Did this data just cross a user, network, disk, or third-party boundary? Yes → validate. No → trust the caller.
2. **Reachability.** Can this project's supported use actually produce this case? Reachable is enough; constructible in principle is not.
3. **Failure semantics.** Can the caller distinguish this failure from real success? If not, throw out of the unit that owns the work — never return a same-shaped default (`0`, `""`, `'free'`, empty list, `MAX_VALUE`).
4. **Named consumer.** Whose live decision changes based on this hash, flag, gate, or ledger? No named consumer → do not build it.
5. **"What would I do differently if this check fired?"** No answer → do not run the check.

A mechanism that fails any test does not ship. When reading cannot settle test 4 or 5, ablate: remove the mechanism, run the check that should fail, and let the result answer. In Audit mode, record the failing test — or the silent ablation — as evidence. **Downgrade** means keep the check that sits on the real trust boundary and drop the inner copy.

## Change discipline

Prevent-mode rails. Every changed line traces to the request.

- Read the task and the code it touches; trace the real flow end to end before choosing a design. Judgement comes after reading.
- Write the simplest complete solution to the stated problem. Nothing speculative, nothing stubbed.
- Reuse before writing: this codebase, the standard library, the platform, an installed dependency. Rewriting what already exists is over-engineering too. Reuse only on contract match: a parser is not a validator — pick the tool whose failure modes fit the request, not the one that merely compiles.
- Occam's razor breaks ties between designs that honor the same contract: fewer entities wins. A flat 60-line function with one state is simpler than three 15-line classes and a registry that wires them. Prove the candidates honor the same contract on the reachable cases before shaving; shaving first is how a stub gets called minimalism. Lines are not entities, but abstraction levels are: a function that mixes orchestration with byte-level detail makes the reader hold both. Split at the level boundary, never to hit a line count.
- When two designs are equally small, take the one that is correct on the reachable edge cases. Equal size is not a license to pick the flimsier algorithm.
- Build without the mechanism first. Add a guard, cache, lock, layer, or parameter only when a check you can run fails without it. If it is already written and you cannot name the check that fails on its removal, remove it now.
- A small diff in the wrong place is a second bug. Fix the shared cause, not the visible symptom.
- If a cheaper design honors the same stated contract, name it in one line and let the user choose. Never silently ship a reduced contract; never use the question to dodge requested hard work.
- Do not abstract single-use code. Inline first; abstract at 3+ real call sites. When a real second implementation or failure window exists, build the harder design — dodging requested work is not simplicity.
- Where judgement is needed, judge. Do not replace it with a scoring table, a checklist, or a re-verification loop over something already settled.
- Do not handle impossible errors. Do not wrap code that cannot throw.
- Expected errors belong at the owned boundary (bad input, declined payment, missing file when absence is legal). Unexpected errors — broken invariants, type lies, "this cannot happen" — leave the unit that owns the work uncaught. Do not invent an out-of-spec recovery the contract never named.
- Do not add backwards-compatibility shims, feature flags, config knobs, caching, or migration machinery unless explicitly requested.
- Future extensibility is a future decision.
- Suppressing the error is not fixing the error. Fail early and visibly. Fail the unit that owns the work: a request fails the request, a job fails the job, a process-level invariant fails the process. Do not catch-and-continue inside the unit whose state is already dirty, and do not abort a whole service for a single request. Recovery belongs to an outer owner that still has a known-good state. Use the isolation the platform already provides; do not scaffold a supervisor, error bus, or retry actor to make throwing safe.
- When a shared helper grows parameters and conditionals so callers run different subsets of it, the abstraction is wrong — inline it back into the callers and re-extract only what remains. Duplication is far cheaper than the wrong abstraction. The first symptom is a boolean or mode parameter whose callers pass literals: the function does two things. Write two functions, or one and let the caller do the differing step.
- A function's name is its contract. `validate` that repairs, `get` that creates, `check` that logs in — the caller reads the name and never learns about the second effect. Put the second effect in a function named for it, or rename.
- Do not hide a root cause with a retry, a longer timeout, a weaker assertion, a broader mock, or a parallel execution path.
- A fallback needs a shipped contract, a named failure mode, and a removal plan — otherwise do not add it. No dual-write, no "if the primary fails, use the sidecar" branch.
- Do not treat agent-authored artifacts (your own schemas, migrations, tests, or docs from earlier turns) as independent evidence that a capability is required. Guarantee mechanisms self-reproduce: hash → schema → migration → docs → "we must keep this now."
- When the chosen design still cuts a real corner with a known ceiling (global lock, O(n²) scan, naive heuristic), leave a comment naming the ceiling and the upgrade trigger: `# ceiling: global lock; per-account locks if throughput matters`. A silent shortcut is under-engineering dressed as simplicity.
- Non-trivial logic (a branch, a loop, a parser, a money or security path) is unfinished until one runnable check exists that fails if the logic is wrong — an assert, a small self-check, or one test in the project's existing harness. That check is not evidence the mechanism must grow.
- Senior engineer test: if you wrote 200 lines and 50 would do, rewrite.
- Leave the lines you changed cleaner than you found them: the misleading name, the stale comment, the branch your change made dead, the commented-out block go with the change. The campground is the lines you touched, not the file — unrelated dead code is a Sweep candidate; mention it, do not delete it.

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

Aggressive minimalism must never remove boundary validation. "Write the fewest lines" is not a license to delete user-input checks. "Let it crash" is not a license to skip owned-boundary I/O handling, or to abort a process for a request-scoped error.

I/O failure handling means the failure reaches the caller as a typed error it can act on. A catch that turns an I/O failure into the permissive answer — an empty set of protected roots, an unknown errno read as "process dead", `EACCES` read as "not found" — is not handling, it is a swallow whose failure direction loosens a protection. The exception does not cover it; Failure semantics does.

A Hard exception in the requested work is in-scope work. Implement it. Do not "simplify" it away to look lean, and do not hide it behind extra wrappers, flags, or fallbacks.

## Stop conditions

Stop when one of these is true:

- Prevent: the change solves the request, and one final sweep of your own diff finds no placeholder-on-failure, trusted-path guard, request-scoped process abort, or speculative machinery left from this change. Ablate the diff as you read it: for each guard, layer, flag, and parameter the change introduced, name the check that fails without it or delete it. Models rarely write cleanly on the first pass but recognize violations on a re-read.
- Audit: every named mechanism has a verdict, including keeps, each with a check that would expose a wrong removal.
- The user set an explicit budget (files, lines, scope) and the honest solution does not fit — stop before editing and report the blocker with the smallest viable alternative.
- A Hard exception is the mechanism under discussion and no separate deletion authorization exists — keep it.
- The user asked for a verdict, not a patch — emit the table only.

In long sessions or right after context compaction, re-run the diff sweep; drift toward defensive bloat and scope growth is monotonic. A cut whose rationale you can no longer state from the proof record is not proved — re-trace it before editing near it. The transcript is not a record; if you find yourself searching it to recall why a `revalidate` or `verify` path was dead, the record should have existed.

## When to read references

Read the one reference the need calls for. Do not load them all by default.

- Classifying and fixing antipatterns (reviewing a diff, cleaning AI slop): read [antipatterns.md](references/antipatterns.md).
- Deciding whether one defensive mechanism stays (focused defensive audit), or running an ablation whose result must count as evidence: read [defensive-audit.md](references/defensive-audit.md).
- Auditing a whole repository or several subsystems for over-defense: read [sweep.md](references/sweep.md) for coverage and proof records and [defensive-audit.md](references/defensive-audit.md) for the per-mechanism verdict. Neither alone covers it.
- Choosing a design shape in Prevent (abstract or not, module boundaries, new dependency, error contract, applying Occam's razor between candidates): read [design-heuristics.md](references/design-heuristics.md).
- Running a Sweep: read [sweep.md](references/sweep.md); it names its own deeper references (investigation, boundaries-and-lifecycle, execution-and-recovery, decision-records, integrating-findings) per phase.

## Deliverables

**Prevent:** no extra artifact. The diff is the deliverable. Mention leftover unrelated dead code in the reply; do not delete it.

**Sweep:** the survey report or change receipt defined in [sweep.md](references/sweep.md).

**Audit:** one verdict row per mechanism.

```text
Mechanism:
Trust boundary it sits on:
Unit that should fail:
Named consumer:
Verdict: keep | remove | downgrade | decide
Evidence:
Check that would expose a wrong removal:
```

**decide** is for a mechanism whose liveness the code and a stated contract disagree on: the architecture doc names it as a gate, nothing constructs it; the changelog calls it advisory, the security doc calls it required. A document is a claim, not a consumer, so the row does not pick a winner — it states both readings and the cut each implies. If the user delegates the choice, decide from the product's shipped core path and release history, then correct the losing artifact in the same change.

Dozens of rows: open with the posture — which boundaries were checked and stay — then group rows by verdict and category, and put the `decide` rows last as the user's list. A report file or host artifact may carry the table; the row fields do not shrink.

Stay read-only until the user authorizes a removal. Authorization to audit is not authorization to delete. Removing a Hard exception always requires its own explicit, separate authorization.

## Maintaining this skill

Add a clause only when it names an agent failure that actually fired; rules are a failure log, not a wish list. If a check can be a linter, formatter, or script, build the deterministic tool instead of spending instruction budget on prose.
