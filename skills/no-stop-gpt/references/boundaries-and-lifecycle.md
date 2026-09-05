# Boundaries and lifecycle

Use when ownership, concurrency, publication, cancellation, or disposal determines
whether a mechanism is redundant. Protected outcomes and authority are defined in
[SKILL.md](../SKILL.md).

## Locate the guarantee

For the selected mechanism, establish relevant origins, owners, mutation rights,
lifetimes, and failure domains. Same-process code may still cross a mutable-state,
plugin, asynchronous, or resource-ownership boundary.

A private borrowed handoff may rely on already enforced guarantees. A hostile
getter, forged typed value, or post-handoff mutation in a test is evidence of an
assumed contract; determine whether supported use promises that contract. Do not
infer either safety or redundancy from process location alone.

## Compare transitions

Map relevant state to its writer, decision-making readers, represented transition,
and failure window. Use a compact event table when ordering is difficult to follow:

```text
Transition | owner | allowed predecessors | terminal effect | late-event behavior
```

Two representations are redundant only when they protect the same guarantee for
the same owner and window. Preserve distinct responsibilities such as atomic
publication, rollback, callback failure containment, terminal outcome arbitration,
process ownership, and durability boundaries.

Where several mechanisms truly mirror one fact, choose the representation that
already owns the guarantee and redirect readers. Remove synchronization glue
instead of creating another coordinator while leaving duplicate truths alive.

## Preserve terminal guarantees

For disposal, establish what the contract promises after completion. A stopped flag
alone may not prevent owned tasks from publishing, writing, retaining resources,
or invoking callbacks. Inspect the relevant timers, listeners, streams, workers,
pending operations, abort paths, and deferred writes.

Consider normal completion, cancellation before or during work, competing terminal
outcomes, partial publication failure, and repeated cleanup where reachable and
affected. Choose observations or checks that can reveal a late effect; do not
require an unrelated lifecycle test matrix for a local change.

A cleanup mechanism may be replaced or deduplicated only if its terminal guarantee
survives. Unknown ordering is a reason to keep or mark unresolved, not to delete
because the happy path stays green.
