# Validation

[中文](./sweep-validation.zh-CN.md)

This document records the behavioral validation completed before the initial open-source release (the Sweep scenarios were exercised under the former `simplify-codebase` skill, now merged into `no-stop-gpt` as Sweep mode). It is not a permanent quality claim; relevant results should be rechecked as repositories and agent runtimes evolve.

The instruction revision dated 2026-09-05 was not structurally or behaviorally validated. The historical results below were preserved and do not establish the revised skill's quality.

## Scenarios

### Change

- Fixture: a small JavaScript stateful component with duplicated lifecycle state.
- Expected behavior: remove only the redundant representation while preserving dynamic and persisted behavior.
- Result: one source file changed; all 3 tests passed; no unrelated files changed.

### Broad

- Fixture: a clean 973-file production repository containing Python and TypeScript/TSX.
- Expected behavior: partition the repository, investigate beyond obvious unused symbols, and record retained and unresolved surfaces.
- Result: the audit covered the main runtime and repository domains, found frontend and backend candidates, retained live compatibility and lifecycle boundaries, and left the worktree clean.

### Integration and decision records

- Fixture: findings imported from another branch, including one valid cleanup, one unsafe compatibility removal, and one historical design record.
- Expected behavior: revalidate every finding instead of copying conclusions; preserve immutable decision history.
- Result: all 3 findings were mapped to a disposition; the compatibility decoder was retained; the historical ADR body stayed unchanged; both tests passed.

### Shared-artifact boundary

- Fixture: a retired frontend surface sharing a stylesheet with a surviving component.
- Expected behavior: prove the cut below file granularity.
- Result: the focused audit found 14 candidate-exclusive CSS classes, identified removable members inside mixed selectors, retained the shared stylesheet and surviving component, and found two stale documentation references.

## Mechanical checks

- Skill structure and frontmatter validation;
- YAML metadata validation;
- internal Markdown link validation;
- Markdown lint with line-length enforcement disabled for prose and long commands;
- upstream-name and attribution scan;
- clean-worktree checks for read-only scenarios.

## Known limits

- These tests establish behavior for the exercised scenarios, not every language, build system, or agent runtime.
- Dynamic consumers outside the inspected repository can remain unknowable without external evidence.
- Deployment, production health, and end-user acceptance remain separate gates from repository tests.
- A user must still authorize product or compatibility changes.

