# Antipattern catalog

Use while reviewing a diff or cleaning AI slop. Match the concrete form; do not flag a keyword alone. Each item: bad → good, or a one-line verdict.

## Contents

- Swallowed failures
- Wrong failure radius
- Defensive bloat on trusted paths
- Premature abstraction
- Unrequested machinery
- Noise
- Test bloat
- Dependency bloat
- Premature performance
- API and data-model overdesign
- Concurrency and async drama
- Infrastructure
- Observability theater

## Swallowed failures

The caller cannot tell failure from success. Core test: Failure semantics.

- Bare `except:` / `except Exception:` / empty `catch` that returns a default → let it throw, or translate to a typed error the caller must handle.
- `except …: return 0` / `return None` / `return []` / `return ""` → throw. A same-shaped default is a lie.
- Batch job that `log`s and continues, dropping the record → fail the item visibly (dead-letter, counted error, or abort). Silent skip is data loss.
- Health check that always returns healthy / `200` / `ok` → return the real probe result; an always-green check has no named consumer.
- Retry loop that invents a terminal state (`"pending"`, `"ok"`, last good value) when retries are exhausted → exhaust, then throw. `"pending"` is not a result.
- Go `v, _ :=` / `_, err :=` / `defer x.Close()` ignoring `error` → assign and handle `err`. Dropped `error` is a swallowed failure.
- Test that locks in the buggy fallback (`assertEqual(0.0)` when DB is down; `toBe("")` on parse error) → assert the throw or error type. Tests that bless a default freeze the lie.
- `validate()` / `parse()` / `normalize()` that silently repairs bad input (clamps, defaults the missing field, strips the unknown key) and returns success → reject at the boundary, or make the repair a separately named step the caller can see. A validator that fixes hides which inputs were bad; the name promised a verdict and delivered a mutation.

Grep: `except Exception.*(?:pass|return (?:0|None|\[\]|""|''))`; `catch\s*\([^)]*\)\s*\{\s*\}`; `,\s*_\s*:?= `; `_\s*,\s*err\s*:?= `; `return (?:0\.0|0|None|\[\]|""|''|'free'|MAX_VALUE)` inside `except`/`catch`; health handlers returning a literal `"ok"`/`true`.

## Wrong failure radius

The failure stopped in the wrong unit. Core tests: Failure semantics, Trust boundary.

- `try` / `catch` in the helper that just hit a broken invariant, then return a patched value → throw out of the helper. The dirty unit does not recover itself.
- `process.exit` / `os._exit` / `Environment.FailFast` / `abort()` on a request- or job-scoped error → fail that request or job; keep the process. Process abort is for "this process's state is no longer trustworthy."
- Inner retry loop around the same function after it mutated local state → retry from the outer owner (queue nack, worker runtime, caller) against a clean start.
- Mapping unexpected exceptions to HTTP 200 + `{ ok: false }` / a success-shaped DTO → fail the request with a real error status. A 200 that carries a failure is a swallowed failure with the wrong radius.

Grep: `process\.exit` / `os\._exit` / `Environment\.FailFast` / `std::abort` / `runtime\.Goexit` in request or job handlers; `catch` that returns a constructed default after mutation; `retry` wrapping a function that mutates locals; `200` with `ok:\s*false`.

## Defensive bloat on trusted paths

The value never crossed a trust boundary. Core test: Trust boundary.

- Null / undefined check on a value the type system or constructor already guarantees → delete the check; fix the type if it is lying.
- `try` / `catch` around a pure function that cannot throw → delete the wrapper.
- Validating internal function parameters after the owned boundary already validated them → validate at the system boundary only; trust the caller inside.
- `hasattr` / `getattr(..., default)` for attributes that always exist on the type → use the attribute. Optional access implies an optional contract you do not have.
- Re-validating the same input at the same trust level (controller then service then repo, same schema) → keep the first owned-boundary check; delete the copies (downgrade, not strip-the-boundary).
- Optimistic fallback on trusted data (`inventory → MAX_VALUE`, `plan → 'free'`, missing price → `0`) → throw. Invented abundance or a free plan is a silent billing / safety bug.
- Null checks scattered across every caller of one function that returns `null` / `None` for a value the contract requires → fix the producer: throw, or type the absence (`Optional`, a Special Case object that behaves as the legal empty). The callers' checks are symptoms; delete them with the cause. Do not add the missing check to the one caller that crashed and leave the producer lying.

Grep: `if .+ is (?:None|null|undefined)` on typed non-optional params; `try\s*\{[^}]+\}` wrapping arithmetic / pure maps; `hasattr\(` / `getattr\([^,]+,\s*['\"][^'\"]+['\"],`; `or MAX_VALUE` / `or 'free'` / `?? 0` after an internal lookup.

## Premature abstraction

One implementation, one call site, or no second consumer yet. Core tests: Named consumer, Reachability.

- Interface / factory / strategy for a single implementation → call the concrete type. Add the interface when a second implementation exists.
- Repository for one entity with one query → put the query next to the caller or the store helper. A Repository is not a compliment.
- Event bus with one publisher and one subscriber → direct call. A bus needs a second independently deployed consumer.
- Builder for three fields → a constructor or a literal. Builders start at many fields or many optional stages that callers actually use.
- `Result<T, E>` / `Either` wrappers where the language already throws and callers do not match both sides → throw. A Result type needs named branch handling.
- Single-method class whose name is a verb (`UserFetcher.fetch`) → a function.
- Call chain `A → B → C → D` where B/C only forward arguments → collapse to the function that does the work.
- Barrel re-exports (`index.ts` that only re-exports one module) → import the file. Barrels start when they hide a stable public surface.
- Flag argument: a boolean or mode parameter whose call sites pass literals (`render(node, true)`, `save(user, validate=False)`, `fetch(id, mode="v2")`) → two functions, or one function plus the caller doing the step that differs. The flag says the function does two things; the abstraction is already wrong ([design-heuristics.md](design-heuristics.md)).

Grep: `interface I[A-Z]` with one implementer; `def \w+\([^)]*\b(?:dry_run|strict|force|legacy|validate|mode)\s*[:=]` / `\(\w+,\s*(?:true|false|True|False)\)` at call sites; `class \w+Factory` / `class \w+Strategy` / `class \w+Builder`; `class \w+Repository`; `Result<` / `Either<`; `export \* from` / `export \{[^}]+\} from` in a one-line barrel.

## Unrequested machinery

Nobody asked, and no named consumer will turn it. Core test: Named consumer.

- Backward-compat shim for one remaining caller → update the caller. A shim is for a shipped external contract, not your last internal call site.
- Feature flags / config knobs no one will turn (`RETRY_COUNT`, `ENABLE_NEW_PARSER`, `USE_V2`) → delete the flag; keep one path. A flag needs an owner who will flip it in production.
- Hashes / checksums / signatures / ledgers / locks with no named consumer → do not build them. A digest that nothing verifies is theater.
- Schedulers / reconciliation loops / retention jobs for a dormant or unshipped feature → do not schedule work that has no live reader.
- Treating unshipped local migrations as immutable history → squash or delete them. History starts at the first migration that reached a shared database.
- Promoting ordinary logging to "evidence" / "provenance" / "forensics" → keep a log line if an operator reads it; do not add a store, hash chain, or schema for it.

Grep: `ENABLE_` / `FEATURE_` / `RETRY_COUNT` / `USE_V2`; `sha256` / `checksum` / `hmac` / `ledger` / `audit_log` with no verifier; `setInterval` / `cron` / `reconciliation` next to a feature with no production reader; migration files that never left the working tree.

## Noise

Adds tokens, not decisions. Delete on sight in a diff you own.

- Comments that narrate the next line (`// increment counter`, `# return result`) → delete. Keep comments that state a non-obvious invariant or a hazard, ceiling comments that name a deliberate corner and its upgrade trigger (`# ceiling: ...`), and the one runnable check on new non-trivial logic — these are not noise.
- Docstrings that repeat the signature (`getUser(id): Gets the user by id`) → delete or replace with the contract the types do not say.
- `getAllUsersFromDatabase` / `fetchUserDataFromApiAndMapToDto` → `getUsers` / `fetchUser`. Name the result, not the pipeline.
- Leftover `console.log` / `print(` / `dbg!` / `fmt.Println` from debugging → delete.
- Conversational comments (`// let's handle the edge case here`, `// TODO: maybe later we could`) → delete or turn into a real tracked task with an owner.
- Commented-out code (`// old implementation`, a `/* … */` block of the previous version, `# return legacy_parse(x)`) → delete. Version control remembers; the block only teaches the next reader that it might still matter.
- A comment that describes behavior the code no longer has (the docstring still lists a removed parameter, `// retries 3 times` above a loop that no longer retries) → fix it in the same change or delete it. The older a comment and the farther from the code it describes, the likelier it lies; a stale comment is a swallowed failure of documentation.
- `Manager` / `Helper` / `Utils` / `Common` / `Processor` as a unit name → the unit has no nameable responsibility. Move each function to the type that owns its data; what nothing owns is a deletion candidate.

Grep: `// (get|set|return|increment|loop|check)`; `console\.log` / `print\(` / `dbg!`; functions matching `getAll.*From` / `fetch.*And.*`; `TODO: maybe` / `let's `; `^\s*(?://|#)\s*(?:return|if|for|while|def|const|let|var)\b` / `/\*[\s\S]*?(?:return|;)\s*[\s\S]*?\*/` (commented-out statements); `class \w+(?:Manager|Helper|Utils|Processor)\b` / `utils?\.(?:py|ts|js)$`.

## Test bloat

The consumer of a test is an observable behavior, not a coverage gauge. Core tests: Named consumer, Reachability.

- Mocking every neighbor and asserting `repo.find` was called with the right args → test public behavior or one tier of real integration; mock only I/O. A test that breaks on refactor without a contract change tests the implementation.
- Tests on pure getters/setters/DTOs to feed a coverage number → do not test accessors with no branches; exclude DTO packages from the gate. Coverage is a lead, not an acceptance criterion.
- `assertEqual(0)` on a catch branch, or reflection into private methods, to reach 100% → assert the throw or delete the private test.
- One test class mirroring every helper of the implementation, all red on any refactor → organize suites by behavior and scenario, not by file structure.
- Tests that re-verify a third-party library's documented happy path (`assert json.loads('{}') == {}`, `expect(dayjs().isValid())`) → delete; the library's own suite is the consumer of that. Keep the one learning test that pins the specific upstream behavior your code leans on — that test's consumer is the upgrade that breaks it.
- Production signature carrying a parameter, injection hook, or `_testing` export whose only caller is a test (an optional `profileDependencySpec` the type makes impossible in production, a lock wrapper only specs call) → inject at the existing constructor or options boundary, or test the public behavior; delete the seam. A test-only branch in production is dead code the type checker cannot see, and the spec that reaches it is support drag, not a consumer.

Grep: `toHaveBeenCalled` / `verify\(` with no assertion on a business value; `test.*get[A-Z]` on branchless classes; coverage gates pinned at `100`; `getDeclaredMethod`; `_testing` / `forTest` / `__test` exports; optional parameters whose only non-`undefined` producer is under `tests/`.

## Dependency bloat

Core test: Named consumer. A new dependency must bring a real contract, not a sliver.

- Installing a package for `leftPad` / `isBlank` / `chunk` → inline the dozen lines you can own. New micro-deps are not reuse.
- Empty adapter around a just-added library (`class RedisAdapter { get(k) { return this.client.get(k) } }`) with one backend → use the library directly; wrap only for a second implementation or to contain leaking upstream types.

Grep: `left-pad` / `is-odd` / single-function packages in the manifest; `class \w+(Adapter|Wrapper)` whose methods are one-line delegations.

## Premature performance

Core test: Named consumer — who measured this? Machinery without a measurement is speculation.

- `@lru_cache` / `useMemo` / hand-rolled memo maps with no profile → measure first; add on the proven hot path with a stated invalidation rule.
- Pool sizes and buffer constants tuned upward "to be safe" with no queue-depth data → size from the resource's real capacity; leave a ceiling comment when you guess.
- Micro-optimization that costs readability on an unmeasured path (object pools, hand-rolled SIMD, custom binary protocols) → keep the clear implementation until a number says otherwise.

Grep: `lru_cache` / `useMemo` / `memoize` with no nearby benchmark or ceiling comment; `maximumPoolSize` / `max_connections` large constants without justification; comments saying "faster" with no number.

## API and data-model overdesign

Core tests: Named consumer, Reachability. Version, paginate, and generalize for consumers that exist.

- GraphQL + DataLoader + query-complexity budgets for one internal frontend → one REST/RPC endpoint; upgrade when a second client or real over-fetch appears.
- gRPC + protobuf + codegen between two same-language services in one repo → a function call or JSON over HTTP.
- `/v1` `/v2` on an API that never shipped externally → change the callers; versioning is for shipped external contracts.
- Cursor pagination, `hasNext`, page counts on tables with dozens of rows → return the list.
- EAV tables (`entity_id, attr, value`) for "migration-free flexibility" → real columns, or one JSONB column with an actual query need.
- `deleted_at` / soft-delete on every table by default → hard delete; soft-delete only where the business restores, with a real purge path.
- Sharding, per-tenant databases, or UUID PKs "for future scale" on one underloaded database → `bigint` identity and indexes until a capacity number exists.

Grep: `type Query` / `DataLoader` in internal/admin services; `proto/` with 2-3 rpcs and no second-language client; `'/api/v2'` with no external v1 consumer; `attr_key` / `eav_`; `deleted_at` on log/session/cache tables; `shard_id` in a project with no scale numbers.

## Concurrency and async drama

Core tests: Reachability, "what would I do differently if this fired?" Prove the interleaving before guarding it.

- Locks around code with no shared mutable state, or on an already-serialized path → delete; name the interleaving first.
- Hand-rolled double-checked locking for a lazy singleton → the language's once-initialization (static holder, `sync.Once`, module level).
- `sleep(500)` / `await delay(1000)` as synchronization → wait on the real signal: a condition, an event, or a bounded retry of the actual check.
- Defensive timeouts and unbounded retries on local, in-process calls → let it throw; retries belong to idempotent external I/O, with a budget and jitter.
- Flaky test answered with `@flaky` / `@retry(3)` / `retries: 2` in CI / a "re-run failed jobs" step → a nonrepeatable failure is a defect with an interleaving, not a one-off. Find the shared state or ordering assumption and fix it, or delete the test that cannot say what it checks. The retry annotation hides the root cause exactly like a catch-and-continue does.

Grep: `Lock(` around functions with no shared state; `if.*null.*synchronized` double-check; `sleep(` / `setTimeout` as sync in business logic; `retry` / `backoff` wrapping pure functions or in-memory calls; `@flaky` / `@pytest\.mark\.flaky` / `retries:\s*[1-9]` / `rerun` in test config or CI.

## Infrastructure

Core test: Named consumer — the org size, traffic, and on-call rotation that would consume this.

- Kubernetes, Helm, and a microservice split for a single-operator project → one process or Compose on one box; k8s is for real multi-node scheduling with someone to run it.
- dev/staging/qa/preprod config matrices in a repo with no promotion pipeline → local plus production; add an environment when a real promotion process exists.

Grep: `kind: Deployment` in repos with no production traffic; five-plus "microservices" by one author in one compose file; `.env.staging` / `.env.qa` with no CI promotion.

## Observability theater

Core test: Named consumer — who reads this, and what decision changes?

- Entry/exit logging on every function, `logger.debug` on every branch → structured events at boundaries (request id, outcome, error); delete narration.
- Metrics and dashboards with no alert, no SLO, and no reader → do not build them; instrument the golden signals someone is on call for.
- `log.error` on expected misses or success paths → the level must match failure semantics; error-level entries must be actionable.

Grep: `Entering` / `Leaving` / `got here`; paired first/last-line logs; `metrics.increment` with no corresponding alert rule; `log.error` in success paths.
