# Design heuristics

Read when a design choice needs more detail than the common principles in
[SKILL.md](../SKILL.md). These are contextual tradeoffs, not numerical gates.

## Abstraction and module boundaries

Prefer concrete code while shared behavior is unclear. Extract when sharing
semantics or hiding a meaningful boundary makes the system easier to understand
and change. Caller count is evidence, not a threshold: a single-use module can
isolate a protocol, resource lifetime, or substantial algorithm.

Compare the obligations an interface introduces with those it removes. A closed
set may need only direct dispatch; a registry needs a current registration or
discovery requirement. Do not preserve unused lower-level entrypoints for imagined callers.

When callers use different subsets of a helper, inspect whether they share a
contract. A boolean can express a coherent option; several flags selecting
unrelated workflows suggest separate functions. Move differing behavior back to
its owners and extract the common part, without mechanically inlining every caller.

## Compare complete designs

Count concepts a maintainer must coordinate: states, types, layers, flags,
dependencies, and assumptions. Prefer the simpler complete design while keeping
reachable edge cases and required protections. No fixed line count determines quality.

Split at a meaningful level of abstraction or ownership boundary. Tiny forwarding
functions can add indirection, but short adapters can provide stable public
contracts. Distinguish them by their callers and responsibilities.

Names should reveal consequential effects. A validator that repairs or a getter
that creates can surprise callers; separate the effects or name the combined
operation clearly, such as `getOrCreate`. A command returning its result is not
inherently a defect.

Keep policy with its owner. Avoid passing options through unrelated layers or
introducing shared mutable state just to shorten signatures. Make callback and
registration connections discoverable using existing conventions rather than
banning event-driven designs.

## Dependencies and reuse

Prefer a contract-matching platform facility or existing dependency. For new
dependencies, compare maintained behavior, footprint, platform support, and
ownership cost with local implementation. Few lines of usage can justify a library
for difficult parsing, security, or compatibility. Copying has maintenance and licensing costs.

An adapter can contain upstream types, resource ownership, errors, policy, or a
substitution boundary with one backend. Forwarding without such a purpose is a
simplification candidate. Prefer an existing seam over production hooks added
only to make implementation-shaped mocks convenient.

Integration checks should protect upstream assumptions the application depends
on. Re-testing generic library examples adds little unless integration or the
actual environment is the uncertain part.

## Error contracts and recovery

Legal absence and idempotent completion can be successes when the contract says
so. Returning an empty query result after a database outage instead conceals failure.

Use the project's established error channel: exceptions, result types, protocol
statuses, or explicit job failures. Translate at the owning boundary when callers
need context or a stable type. Do not add wrappers just to make all errors look alike.

Recover where the owner can establish valid state. A broken invariant may fail a
request, job, worker, or process depending on what was corrupted. Use existing
isolation; additional recovery infrastructure needs a current requirement.

Retries need a recoverable failure mode, safe repeated effects, a bounded budget,
and visible terminal failure. Fallbacks need defined degraded behavior that callers
can distinguish where necessary. Temporary compatibility and rollout paths need
end conditions; permanent product fallbacks need not have removal dates.

## Resolve uncertainty proportionally

Start with the contract and affected code. Use measurements for claimed performance
benefits and lifecycle reasoning for locks or cancellation. Known capacity or
correctness requirements can justify a design before a benchmark exists.

Use [ablation](defensive-audit.md#6-use-ablation-when-it-can-resolve-uncertainty)
when a bounded comparison could settle the remaining question. Do not require
building a knowingly incorrect version before implementing a necessary guard.
No observed difference supports only the exercised cases.

Record a material ceiling and upgrade trigger when the tradeoff would otherwise
be hidden. Avoid speculative implementation or mandatory comments for every future
limit. Routine reversible choices remain with the executor under [SKILL.md](../SKILL.md).
