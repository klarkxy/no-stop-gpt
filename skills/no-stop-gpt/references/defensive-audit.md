# Focused defensive audit

Use when asked to decide whether **one** defensive mechanism (guard, test, retry, fallback, copy, rollback, compatibility shim) is still necessary. Stay read-only unless the user explicitly authorizes a change.

Key terms: **borrowed handoff**, **owned boundary**, **boundary theater**.

## 1. Locate the real boundary first

For each validator, copy, retry, rollback, or guard, name:

- origin of the value
- current owner
- next owner
- mutation rights
- lifetime
- failure domain

Do not classify the mechanism until those six are named. "It looks defensive" is not a location.

## 2. Borrowed handoff vs owned boundary

**Borrowed handoff** — same process, private, types and ownership already enforced by the component boundary. Defense here that only prevents "this cannot happen" behavior is removable.

**Owned boundary** — untrusted input, config parsing, model/tool JSON, queues, storage, network protocols, plugins, workers, subprocesses, or data crossing asynchronous lifetime boundaries. Defense here is part of the contract. Keep unless proven otherwise.

## 3. Boundary theater is the primary prey

**Boundary theater** is validation, copying, rollback, or hostile-object defense sitting on a trusted handoff rather than a real trust boundary.

Examples of theater: re-validating a struct the constructor already made; defensive-copying an object the owner will not mutate; catching exceptions a pure helper cannot raise; treating a teammate's in-process call like hostile plugin input.

Theater is not "any check." An owned-boundary check with no inner copy is not theater.

## 4. Redundancy needs the same window

Two mechanisms are redundant only when they share:

- the same owner
- the same transition
- the same failure window

Similar names are not enough. `validateUser` in the HTTP adapter and `validateUser` in a worker that reads the queue are different owners and different trust boundaries.

## 5. Trace, do not keyword-hunt

For every candidate, trace:

- producer
- consumer
- failure model
- trust boundary
- the unit that should fail (request, job, worker, process)
- the live decision the mechanism changes

Never flag `hash`, `fallback`, `gate`, `retry`, `lock`, or `checksum` by themselves. No named consumer → the mechanism is a candidate for removal. A named consumer whose live decision changes → keep or downgrade, with evidence.

Do not treat agent-authored schemas, migrations, tests, or docs as a named consumer. A test that only asserts the mechanism exists is not a consumer of a live decision. The one runnable check on new non-trivial logic is not boundary theater; do not flag it for removal.

## 6. Ablate before you judge

When the trace leaves a named consumer or a live decision uncertain, ablate: remove or disable the mechanism, run the check that would fail if it mattered, and observe. An ablation that changes nothing observable is the evidence for remove; one that breaks something is the evidence for keep and names the consumer you missed. Run it on a branch or in a test environment — the audit stays read-only for the user's tree.

Rules that make the result count as evidence:

- One mechanism per experiment. Removing two things and seeing one failure proves nothing about either.
- Hold everything else constant: same inputs, fixtures, data, and environment as the control run. A different run is a different experiment.
- The check must be able to fail. Before ablating, confirm the probe actually reaches the mechanism — a fixture that exercises the branch, a request that hits the boundary, a corruption the assertion would catch. If no such probe exists, write the one probe first; a green suite that never reached the mechanism is silence, not proof.
- Trust boundaries produce false negatives by construction. A happy-path suite stays green after authz, input validation, or I/O failure handling is removed, because the suite never sent the hostile case. That is the suite's blind spot, not a verdict. Ablation evidence never removes a Hard exception on its own; the probe for a boundary mechanism must be the adversarial case, and even a silent ablation there only reports a missing test.
- Record the ablation in the verdict: what was removed, which probe ran, what changed. That record is the "check that would expose a wrong removal."

## 7. Verdict record

One row per mechanism:

```text
Mechanism: <symbol or site>
Trust boundary it sits on: <owned boundary | borrowed handoff | none>
Unit that should fail: <request | job | worker | process>
Named consumer: <who, what decision> | none
Verdict: keep | remove | downgrade
Evidence: <producer, consumer, failure model, live decision — or which Core test failed>
Check that would expose a wrong removal: <smallest test, probe, or trace>
```

- **keep** — owned boundary, or a borrowed-handoff check that still changes a live decision in a reachable failure. A catch at the request or job boundary that becomes a typed error the caller or operator sees (4xx, job failure, dead-letter) is keep.
- **remove** — no named consumer, unreachable case, or theater on a borrowed handoff. A catch, retry, or fallback inside the unit that just failed, inventing a patched value so execution can continue, is remove.
- **downgrade** — keep the owned-boundary check; delete inner copies, same-level re-validation, or same-shaped defaults. A process abort on a request- or job-scoped error downgrades to failing that request or job; keep process abort only when the process itself is untrustworthy (see Domain portrait).

The "wrong removal" check must be able to fail if the verdict is wrong. "Looks unused" is not a check. It must also fail a happy-path-correct, adversarial-unsafe stand-in — the lean version that breaks on the trust-boundary case.

## 8. Domain portrait — when heavy defense is the optimum

Do not strip assertions, paired invariants, bounded loops, or static-allocation discipline from a system whose wrong answer is worse than a crash: a financial system of record, a storage or consensus kernel, avionics or other safety-critical control, or any component that must stay correct under bitrot, replica divergence, or physical-world fault.

The portrait, in full or mostly:

- correctness failure costs far more than availability failure
- this process is the source of truth, or the only place corruption can be detected
- the failure model includes bit flips, disk corruption, replica divergence, or hardware drift — not merely a teammate's bad call
- assertions are load-bearing in the verification strategy (simulation, fuzzing, production crash-on-violation)

In those domains a production assertion that crashes on a violated invariant is a Hard exception: it downgrades a correctness disaster into a liveness failure. This portrait does not license assertion theater on a CRUD trusted path — apply it only when the process is the source of truth and the failure model includes corruption. A request-scoped or job-scoped error is not this portrait; do not cite it to justify aborting the process in a request handler.

An error defined out of existence at design time (the API contract makes the edge a legal success) is not a swallowed failure; audit the contract, not the missing throw.

## 9. Removal authorization

- The audit itself is read-only. Emit verdicts; do not edit.
- Deletion requires an explicit user authorization for that mechanism (or an explicit "apply these verdicts" instruction).
- Deleting a Hard exception (authz, trust-boundary validation, security isolation, crypto, data-loss prevention, stored-format compatibility, accessibility essentials, external I/O failure handling, startup config validation, quiescence-establishing cleanup) is always its own explicitly authorized objective. An audit of "is this retry needed?" is not authorization to strip authz.
- Quiescence cleanup is never theater: a dispose/shutdown path that guarantees no owned timer, listener, worker, or pending task can still publish or mutate after the terminal boundary protects a real failure window, even though it sits inside one process.
- If the authorized removal is one mechanism among many accidental-complexity cuts, finish this verdict and escalate the rest to Sweep mode ([sweep.md](sweep.md)).
