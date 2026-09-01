# Design heuristics

Read when choosing a design shape in Prevent mode: whether to abstract, how to shape a module, whether to add a dependency, how to shape an error contract. Every heuristic serves the optimum — the design a senior engineer would defend — not minimal line count.

## When to abstract

- Write the concrete cases until they run. Compress only after a second real site shares semantics. The 3+ call-site rule stays the default gate.
- Even at 3+ call sites, wait for a cut-point: a narrow interface that hides real complexity behind a few functions. While the system's shape is still fluid, bias toward waiting — a wrong early cut costs more than late duplication.
- The abstraction must be smaller than the duplication it removes. If the helper plus its indirection outweighs the repeated lines, it is overhead, not design — inline it.
- Premature flexibility is worse than premature optimization: optimization can be deleted, flexibility grows dependents that freeze the wrong shape. Do not build a plugin, registry, or loader for a closed known set; write the three call sites.
- When you extract a higher-level helper, keep the lower entry point. Callers who need a different grain must not be forced through a hole.

## When the abstraction is already wrong

The tell: a shared helper grows parameters and conditionals so different callers run different subsets of it.

Recovery is backward: inline the helper into every caller, delete the parts each caller does not use, then re-extract only the duplication that remains. Duplication is far cheaper than the wrong abstraction. Do not protect the sunk cost.

## Module shape

- Prefer a deep module: small interface, substantial hidden work. Reject a shallow one whose public surface outweighs the complexity it hides. A new function must add capability, not another hop.
- The bigger the interface, the weaker the abstraction. Do not grow an interface to look complete.
- Do not split a coherent unit into many tiny classes that each add a sliver of behavior (classitis). More types is not more design; collapse them into the type that owns the work.
- Pull complexity down into the module that owns the problem. Do not export a knob, exception, or config flag just to keep the implementation pretty. A simple interface beats a simple body.
- Do not thread a value through signatures that never use it (pass-through variables). Put it on an object the ends already share, or keep it at the owner.
- A unit's behavior must be obvious from that unit (locality of behavior). Hiding the implementation behind a named call is fine; hiding that the call happens — in a distant file, event wire-up, or naming convention — is not.
- Optimize for simple (unbraided concerns), not easy (familiar or nearby). Do not complect state with time, policy with mechanism, or what with how just to shrink the file count. More straight pieces beat fewer knotted ones.

## Dependencies

- Do not add a new dependency for a handful of lines you can own. Copy the sliver; if the copied definition must stay honest, lock it with a test that imports the upstream in test scope only. This targets new micro-deps — an installed, contract-matching library is still reuse.
- A thin wrapper around a third-party library is justified only by a real second implementation or by containing upstream types that would otherwise leak into your API. A one-backend adapter that forwards every call is dependency theater.

## Error contract shape

When an edge case is a normal completion of the stated goal (deleting a file that is already gone; clamping an out-of-range substring), define it as success in the API instead of inventing an exception surface. This is a contract change made at design time — not a swallowed failure. Real failures still throw, and the caller must never mistake failure for success (Core test 3).

## Economics of speculation

A presumptive feature pays four costs even when the guess is right: build (labor for unused work), delay (the requested work ships later), carry (every later change pays the extra complexity), and repair (the guess ages before the real need arrives). Name which cost you are accepting before keeping speculative machinery. This is evidence language for Audit verdicts, not a license to drop a requested contract.
