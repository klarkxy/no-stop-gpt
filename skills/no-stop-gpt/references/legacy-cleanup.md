# Delete legacy code: cut, repair, repeat

Use for authorized removal of legacy code, abandoned designs, stale flags, and
their support artifacts. [SKILL.md](../SKILL.md) owns authority, scope, and
protected outcomes; [sweep.md](sweep.md) covers broader subsystem simplification
and design-record consolidation. Begin with a useful cut rather than waiting
for repository-wide proof that nothing can break.

## Find the first cut

Start with a replaced implementation, completed migration, abandoned design,
obsolete flag, duplicate path, or unused-code finding. Search for its names,
paths, configuration keys, and registrations. Read the relevant entrypoint and
current replacement. Use existing compiler, linter, reference, and dependency
tools; no particular tool or full call graph is required.

For example, adapt these searches to the actual candidate:

```sh
git status --short
rg -n 'OldParser|useOldParser|old-parser' src tests scripts
rg -n 'legacy|deprecated|compat|fallback|TODO.*remove' src
```

Names identify candidates, not verdicts. An unused export may still have internal
uses or initialization effects. Check likely dynamic loaders, package exports,
and build scripts when relevant; do not expand every candidate into an exhaustive
investigation. If a reference tool appears blind, query a known active symbol
before trusting empty results. Use another check if the tool is unavailable.

## Make the cut and follow the failures

1. **Keep the edit recoverable.** Inspect existing changes and preserve unrelated
   work. Use the diff or an isolated workspace; no mandatory commit per cut.
2. **Delete a coherent old path.** Remove its exclusive helpers and support
   artifacts together. Delete implementation outright rather than commenting it
   out, moving it to a backup directory, or hiding it behind another flag.
3. **Run a check that can reveal dependencies or regressions.** Choose the relevant
   compiler, tests, build, or actual workflow. Capture a baseline when an existing
   failure would otherwise be hard to distinguish.
4. **Use failures as the work list.** Repair callers and surviving behavior using
   the recipes below, instead of restoring the old path on the first failure.
   Continue autonomously; routine repair is part of the task.
5. **Chase the newly dead code.** Rescan after each coherent batch. Follow the
   removed path through imports, exports, types, parameters, dependencies, fixtures,
   configuration, scripts, and docs until its exclusive support is gone.
6. **Finish the requested scope.** Verify the combined result. One passing batch
   is a checkpoint, not permission to leave the other requested areas untouched.

Do not require zero uncertainty before trying a local cut. A missing fact only
blocks the dependent action when it concerns a requirement or consequence that
the local attempt cannot resolve. Source rollback does not undo data deletion or
an externally deployed behavior change; those remain separate actions.

## Recipes

| Candidate | Cut | Repair and follow-through |
| --- | --- | --- |
| Unused export, function, or file | Remove an unused export only if the implementation still has local uses; otherwise delete the declaration or file. Preserve required initialization effects. | Fix surviving imports and barrel exports. Rescan for newly unused locals, types, and dependencies. |
| Mutually referencing old files | Identify whether any surviving entrypoint reaches the group; internal references alone do not keep it alive. Delete the disconnected group together. | Check route, plugin, and script registration, then remove group-only fixtures and packages. |
| Stale feature flag | Once its final behavior and retirement are established, keep the winning branch and remove the other branch and condition. | Remove the flag reader, exclusive parameters, config, and branch-only tests. Check that no supported deployment still needs the switch. |
| Old compatibility wrapper or alias | Redirect in-scope callers to the current interface and delete the wrapper. | Fix signatures at callers instead of adding another adapter. Keep only compatibility still needed by supported external consumers or stored data. |
| Duplicate implementation | Pick the implementation matching the required behavior; migrate callers and delete the other. | Bring over any unique required behavior. Remove duplicate state, synchronization, and selection switches made unnecessary by consolidation. |
| Tests keeping an obsolete path alive | Delete tests and fixtures whose only purpose is the retired implementation. | Move unique behavior coverage to the surviving entrypoint. Test-only references neither automatically save production code nor automatically condemn it. |
| Old config, dependency, or build hook | Remove it when its consuming path is gone; check scripts and build-time use too. | Update the manifest and lockfile with the existing package manager; remove exclusive CI steps, examples, and current documentation. |
| Abandoned names and comments | Search the old terms and rewrite current descriptions around the surviving design. | Remove stale TODOs and repair links. Preserve licensing, useful rationale, and immutable historical records. |

## Repair without rebuilding the old design

Read the first meaningful failure and classify it:

- **Missing import, symbol, or parameter:** migrate the real caller or delete the
  caller if it belongs exclusively to the retired path. Avoid a forwarding stub
  added solely to silence the compiler.
- **Test tied to implementation shape:** replace it with a check of surviving
  behavior, or delete it if its only subject was intentionally retired. Do not
  preserve a dead subsystem merely to keep its own tests green.
- **Required behavior regressed:** repair that behavior in the surviving design.
  If the cut was wrong, restore the necessary portion and continue other cuts.
  Do not weaken the test or restore the entire old subsystem reflexively.
- **Failure already present before the cut:** record the limitation and continue
  independent cleanup. Do not claim that the affected behavior was verified.

Do not answer a failed deletion with new fallbacks, flags, adapters, or empty
implementations unless they serve a concrete remaining requirement. A failed check
is not a completed cleanup. Keep repairing, reduce the batch to locate the cause,
or undo the specific mistaken edit while preserving unrelated progress.

### Example: replace an internal parser

Suppose the current parser replaces an old internal parser and all callers are
within scope. Delete the old parser and selector. Compilation reveals two callers
still passing `legacyMode`: migrate them to the current signature and delete the
parameter. Tests reveal an input case supported only by the old parser: implement
that required case in the current parser and keep a behavior test there. Remove
old-parser-only mocks and fixtures. Rescan; if the parsing package now has no
runtime, tooling, or build consumers, remove it and update the lockfile.

The result is one parser handling the required inputs. Restoring the selector or
adding a catch-and-fallback to the old parser would leave the cleanup unfinished.

## Close out

Search the old symbols and keys again; inspect remaining hits rather than requiring
historical docs to contain none. Check the affected surviving workflows and the
combined diff. Measure completion by retired paths and working behavior rather than
deleted-line targets, and do not stop at import cleanup when the request was to
retire a whole implementation. Deliver the self-contained summary of retired paths,
repairs, checks, and remaining blockers required by [SKILL.md](../SKILL.md).

See [sources.md](sources.md#legacy-code-deletion) for the adopted references.
