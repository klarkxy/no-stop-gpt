# Antipattern catalog

Use concrete examples to investigate a diff, not to prescribe deletion from a
keyword. Apply [SKILL.md](../SKILL.md) for authority and protected outcomes.
The examples below are conditional: supported contracts, ownership, platform
semantics, or failure windows can justify an otherwise suspicious form.

## Establish the diff scope

Establish the requested comparison and include relevant new files; ordinary diffs
omit untracked files. An empty diff is not permission to widen the review. Judge
preservation against the user's intended result and current supported contract:
deleted lines can contain the bug being fixed.

## Swallowed failures

Investigate catches that return `0`, an empty list, a free plan, or a cached value
when the caller would interpret that as successful fresh work. Preserve legal
absence and explicitly defined degraded results; expose actual failures through
the established error channel.

- A failed query reported as an empty result conceals an outage.
- A batch that logs and silently drops records loses the caller's failure signal;
  use its existing item-failure or batch-failure contract.
- A health endpoint returning a literal may be a valid liveness probe, but cannot
  stand in for a promised dependency-readiness check.
- Ignored errors deserve inspection when completion or cleanup can fail meaningfully;
  language syntax alone does not prove an actionable loss.
- A normalizer may intentionally repair input. A validator promising rejection
  should not silently normalize unless that behavior is explicit in its contract.

Useful search terms: empty catches, default returns in error branches, ignored
error values, and health handlers. Read the surrounding failure path before judging.

## Wrong failure radius

A request error should not terminate a healthy process. A corrupted process should
not continue by inventing a local success. Inspect the state affected and who can
establish a clean recovery point.

Retries after partial mutation need a valid restart or compensation contract.
Protocol error envelopes can be legitimate when clients recognize them; do not
replace an established wire format merely because it differs from preferred HTTP
status conventions. Fix ambiguity where the caller can mistake failure for success.

## Repeated defense

Null checks, defensive copies, and repeated validation may be redundant when the
same owner already maintains the exact guarantee. They can remain necessary for
mutable values, untyped callers, plugin inputs, or later asynchronous transitions.

If callers repeatedly guard a producer that violates its required return contract,
fix the producer rather than adding another symptom check. Preserve genuinely
optional values and distinct boundary validation. See
[defensive-audit.md](defensive-audit.md) for the evidence needed to distinguish them.

## Premature abstraction

Interfaces with one implementation, short adapters, event buses, builders, result
types, and forwarding functions are leads only when their purpose is unclear.
Look for a stable interface, resource owner, construction constraint, integration
requirement, or coherent error contract before proposing direct code instead.

Mode flags selecting unrelated workflows can indicate a poor abstraction. A simple
boolean option or a platform-required interface is not inherently wrong. Prefer
shared semantics over shared syntax; see [design-heuristics.md](design-heuristics.md).

## Unrequested machinery

Investigate flags with no operational owner, digests with no verifier, schedulers
with no work, or parallel representations with no distinct requirement. Trace the
supported behavior before removing them.

An internal compatibility caller might be migrated together; external consumers
or persisted formats need their compatibility obligation preserved. Unshipped
migrations are not automatically disposable: establish whether shared databases,
branches, or repository conventions already rely on their identity.

Do not turn diagnostic logs into new stores or hash chains without a current
requirement. Conversely, an audit trail with a real consumer is not merely logging.

## Noise and misleading names

Within the authorized change, remove stale or commented-out implementation and
comments that only narrate syntax. Keep licensing notices, important rationale,
non-obvious invariants, and operational diagnostics.

Names should reveal relevant behavior without narrating the whole pipeline.
`Manager` or `Utils` may suggest unclear ownership but is not evidence to delete
its contents. Debug output may be disposable; command output and useful diagnostic
messages may be part of the product.

Leave unrelated cosmetic cleanup outside the assignment.

## Shorter expressions with different behavior

Check accepted inputs, evaluation order, and effects before replacing an explicit
branch with shorthand or an existing helper. For example, `value || fallback`
also replaces `0`, `false`, and `""`, while `value ?? fallback` replaces only nullish
values in JavaScript. Neither is a universal cleanup of the other. Compare error
types and absence values too; similar names do not establish helper equivalence.

## Test bloat

Investigate checks that mirror helpers, assert incidental call order, or exist
only to raise coverage. Keep interaction assertions when the interaction itself
is the contract, such as preventing duplicate side effects.

Judge an assertion by the distinct regression it can detect. When status and
error code already identify the required failure, pinning incidental prose may
add no coverage. Wording and constant assertions can still protect serialized
values, stable identifiers, redaction, or required diagnostics; distinguish an
external contract from a copy of the implementation before removing them.

Contract tests should observe the interface consumers actually use. Helpers may
explicitly extract a required response field, but should fail when its shape is
wrong. Silently renaming fields, accepting old and new envelopes, or filling
missing values can hide a broken contract. Test intentional compatibility at its
production boundary rather than recreating it in the test harness.

Prefer observable behavior and existing test seams. Test-only production hooks
may be unnecessary, but a seam enabling deterministic time, fault injection, or
isolation can serve a real verification requirement. Do not remove it because
its direct caller is a test.

Generic library examples add little; checks protecting the application's actual
integration or environment can be useful. Do not change coverage policy, delete
flaky tests, or weaken assertions merely to make a cleanup pass. Diagnose the
failure and preserve unique behavior coverage.

## Dependency and performance overhead

Look for repeated reads, per-item requests where a matching batch operation exists,
or updates that notify consumers despite unchanged state. Check snapshot freshness,
ordering, failure handling, and notification semantics before eliminating work.
Prefer removing waste over adding a cache or worker pool.

A tiny package or thin wrapper can cost more than the responsibility it removes;
a maintained implementation can also prevent subtle local bugs. Compare semantics,
maintenance, platform support, and existing use before replacing it.

Caches, pools, memoization, and custom protocols need a workload or contract reason.
Measurements are needed to claim improvements, but known capacity and semantic
requirements can justify mechanisms before profiling. Preserve identity guarantees
and invalidation behavior when changing a cache. Do not treat missing nearby
benchmark comments as evidence that no justification exists.

## API and data-model complexity

GraphQL, gRPC, pagination, versioning, EAV, soft deletion, UUIDs, and sharding are
not antipatterns by name. Investigate the obligation each adds and the supported
consumer or guarantee it serves.

A small internal API may use a simpler existing path. A large or externally
consumed one may need stable pagination, compatibility, or generated contracts.
Soft deletion can support restoration and retention; identifiers can support
offline creation or non-enumerability. Do not change data semantics or retire
supported interfaces as incidental cleanup.

## Concurrency and retries

A lock without shared state or a reachable ordering concern may be redundant.
Establish the actual interleaving, including resource ownership, before removal.
Prefer existing once-initialization and synchronization facilities when they fit.

Sleeping in place of a readiness signal is often brittle. A bounded observation
loop can be appropriate where the platform exposes no event. Local timeouts can
protect liveness, and retries can handle contention; process location alone does
not decide necessity. Repeated effects must remain safe and termination visible.

Test retries can hide an unresolved failure. Preserve the failure evidence and
investigate it rather than treating eventual success as repair or deleting the
test without understanding its guarantee.

## Infrastructure and observability

Deployment platforms, environments, queues, and services need current operational
requirements. Team size and traffic alone do not establish whether those needs
exist. Inspect deployment and ownership before proposing consolidation; do not
turn a code cleanup into an unrequested infrastructure migration.

Logs, metrics, and dashboards should support diagnosis or decisions. They need
not each have an alert or permanent reader to be useful. Remove repetitive
narration where it adds noise, preserve required audit and failure evidence, and
choose severity according to the actual outcome.
