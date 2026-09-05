# Integrating findings

Use when combining findings or changes from another branch, task, or review.
Apply [SKILL.md](../SKILL.md) authority and assess the destination's current contracts.

## Establish source and contribution

Use each source's merge base or recorded filesystem basis to distinguish its
contribution from changes it inherited. Identify findings, patches, rationale,
and observations attributable to the source. Treat stale conclusions as hypotheses.

For each relevant candidate, establish whether its symbols, consumers, dynamic
paths, and compatibility obligations still match the destination. Inspect history
or additional paths when they could change the conclusion, rather than repeating
a full repository audit for every imported item.

Retain useful evidence, consolidate overlaps under the current contract owner,
and reject duplicates or conclusions invalidated by current behavior. Preserve
material counterarguments and uncertainty. Distinguish a proposal from an applied
change and distinguish both from behavior that was actually exercised.

## Integrate within authority

Apply authorized code and documentation according to current ownership and
behavior, not source arrival order. Cover interactions introduced by combining
changes with the relevant checks described in
[execution-and-recovery.md](execution-and-recovery.md). Isolated results do not
establish the combined result; unchanged independent evidence need not be rerun
without a reason. Honor explicit verification constraints and state their limits.

Classify requested source findings as retained, consolidated, rejected, superseded,
or unresolved, with enough explanation to understand the result. Small imports
need a short account; large imports need a durable mapping. Finding counts are
not a preservation target.

If updating an authorized pull request, describe the retained scope and actual
validation state. Do not represent unresolved work as ready. Posting, closing,
publishing, or otherwise changing external reviews requires user authority;
permission to inspect their findings alone is insufficient.
