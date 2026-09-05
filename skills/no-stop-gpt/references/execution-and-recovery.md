# Execution and recovery

Finish the authorized simplification and preserve the surviving contract. Follow
[SKILL.md](../SKILL.md) for permissions and [Sweep](sweep.md) for scope.

## Choose a reviewable batch

Group changes by the behavior or ownership boundary they retire. Include affected
callers, tests, and documentation even when they live in other directories. A batch
is an integration unit, not a reason to stop before the remaining authorized work.

If a candidate depends on unknown external use, unsupported data conversion, or
an unresolved product choice, hold that cut and state the missing fact. Continue
independent supported cuts. A failing baseline limits the evidence but does not
by itself make every unrelated change impossible.

When already-authorized workers are involved, give shared artifacts one owner and
integrate against current contracts. Worker success establishes only its reported
scope; choose relevant checks for the combined result. This reference does not
require delegation or create a separate orchestration workflow.

## Retire the complete obligation

Follow affected declarations through registration, dispatch, implementation, state,
imports, exports, generated inventories, examples, documentation, and tests. Include
only surfaces tied to the cut. Preserve unrelated working-tree changes.

Remove compatibility glue only when its obligation is absent or its retirement
has been authorized with a suitable transition. Update the surviving state owner
instead of adding another synchronization layer. Preserve unique behavior checks;
remove tests that exclusively pin behavior intentionally retired.

For source-only work, the diff can be the recovery path. Data, configuration,
deployment, or publication effects need recovery appropriate to those effects
and user authority for the action. A cleanup request is not publication permission.

## Choose relevant checks

Select checks that could expose a real failure in this change, honoring user
constraints. Depending on the cut, these may be residue searches, a compiler or
analyzer, existing tests, a build, a protocol comparison, or a real workflow.
Capture a baseline when it helps distinguish pre-existing failure from regression.
Do not mechanically run every category for every batch.

Broader integration checks are useful when the change crosses contracts; controlled
measurements are needed to claim a performance improvement. Ablation is optional,
using the conditions in [defensive-audit.md](defensive-audit.md#6-use-ablation-when-it-can-resolve-uncertainty).

Once relevant checks pass, repeat or broaden them only after a new change, failure,
or unresolved concern. Source inspection, test success, build success, deployment,
and real operation are different evidence levels; report only those reached.

If a check fails, determine whether it exposes a regression, a pre-existing issue,
or a mistaken removal premise. Repair or undo the affected change. Do not weaken
a meaningful assertion, enlarge a timeout, or retry blindly to make the cut pass.

## Complete and report

Finish affected callers and artifacts, then account for all authorized boundaries.
Report concrete blockers without presenting an initial slice as the whole result.
A user-imposed verification limit should be stated, not bypassed by extra checks.

Small changes need a short account of what changed, relevant evidence, and limits.
For broad or consequential changes, retain enough detail to support continuation:

```text
Scope and obligation retired:
Affected artifacts and surviving owners:
Preserved behavior and authorized behavior changes:
Evidence reached, checks performed, and relevant baseline failures:
Remaining candidates, uncertainty, or blockers:
Recovery for material side effects:
```

No separate receipt file is required when the diff and response carry the needed
information. After compaction, restore completed work and pending decisions;
reinvestigate only missing or invalidated evidence.
