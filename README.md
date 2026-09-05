<div align="center">

# No, Stop! GPT!

[![Agent Skill](https://img.shields.io/badge/Agent-Skill-22c55e?style=flat-square)](./skills/no-stop-gpt/SKILL.md)
[![License: SATA 2.1](https://img.shields.io/badge/License-SATA%202.1-0f172a?style=flat-square)](./LICENSE.txt)

[简体中文](./README.zh-CN.md)

<img src="./assets/mascot.png" alt="No, Stop! GPT! mascot — GPT-chan caught mid-over-engineering" width="240">

</div>

Coding agents can turn small changes into unnecessary factories, duplicate state,
and fallback paths that hide failure. This skill helps keep complexity proportional
to the requested behavior and its real boundaries. It also guards against the
opposite mistake: dropping requirements or leaving hard work unfinished.

## Three modes

| Mode | Purpose | Authority |
|---|---|---|
| **Prevent** | Lightweight principles for substantive code changes | Uses the coding task's existing scope |
| **Audit** | Judge a mechanism, diff, or PR with evidence | Review requests are read-only |
| **Sweep** | Simplify a subsystem or repository | Survey is read-only; explicit cleanup requests authorize scoped changes |

Routine Prevent work needs only the main skill file. References provide design
examples, focused audits, or deeper cleanup guidance when the task needs them.

### Decision principles

Consider the relevant dimensions rather than running a checklist on every line:

- **Boundary and ownership:** input trust, mutation, lifetime, concurrency, and failure ownership.
- **Reachability:** cases supported use can produce, including dynamic and external consumers.
- **Failure semantics:** callers can distinguish real failure from legal success or absence.
- **Consumer and purpose:** the behavior or guarantee a mechanism serves.
- **Decision value:** what new evidence would change the choice.

Prefer fewer concepts among designs that satisfy the same requirements. Caller
counts and line counts are clues, not design rules. A single-use abstraction can
protect a useful boundary, and two similar checks can protect different failure windows.

Use contract and consumer evidence first. Ablation is an optional bounded experiment
for suitable unresolved questions. Passing checks support only the cases exercised;
they do not establish that no consumer exists or that a deletion is safe.

Preserve necessary security, data integrity, compatibility, accessibility, and
lifecycle guarantees. Redundant implementations may be simplified when the same
outcomes survive. Full guidance: [SKILL.md](./skills/no-stop-gpt/SKILL.md).
## Install

With the open [skills CLI](https://github.com/vercel-labs/skills):

```bash
npx skills add klarkxy/no-stop-gpt
```

Or copy `skills/no-stop-gpt/` into your agent's skills directory:

```powershell
# OpenAI Codex
Copy-Item -Recurse skills/no-stop-gpt ~/.codex/skills/
# Claude Code
Copy-Item -Recurse skills/no-stop-gpt ~/.claude/skills/
# Cursor (user level)
Copy-Item -Recurse skills/no-stop-gpt ~/.cursor/skills/
# Shared (Codex / Cursor / Kimi Code CLI all read this)
Copy-Item -Recurse skills/no-stop-gpt ~/.agents/skills/
```

For a project-level install, drop it in the repo's `.cursor/skills/`, `.claude/skills/`, or `.agents/skills/`.

## Usage

- Invoke `no-stop-gpt` explicitly, or let a host that supports automatic discovery
  select it for substantive coding work. Installation does not guarantee activation.
- Ask "review this diff for overengineering" or "is this retry necessary?" for
  read-only conclusions: **keep**, **remove**, **downgrade**, or **decide**. Simple
  questions get concise evidence; complex findings get structured records.
- Ask "audit the repository for unnecessary defense" for a Sweep survey covering
  the requested domains, including retained candidates and unresolved questions.
- Ask "simplify this subsystem" or "apply these findings" to authorize investigation
  and changes within that scope. Intermediate findings do not require another
  approval. Mode switches do not reset existing authorization.
- Routine reversible choices stay with the executor. Changes to requirements,
  compatibility, authority, or irreversible effects need direction if not already
  authorized. Publication and deployment are separate actions.

Completion follows the request: finish the implementation or all authorized cleanup
boundaries, using relevant checks within user constraints. Do not repeat successful
checks without new changes or uncertainty. A read-only audit completes with its
answers; no justified deletion is a valid result.

Pure explanation, formatting, and unrelated prose edits do not trigger the skill.
Security or storage in ordinary code does not exclude proportionality guidance,
but specialist security or migration work must not be redirected into cleanup.

Historical validation is recorded in [docs/sweep-validation.md](./docs/sweep-validation.md).
Those results cover the earlier version, not this revision. No structural or
behavioral validation was performed for this revision.
## Credits

The rules are adapted from 2025–2026 community practice and classic texts. Thanks, in no particular order:

- Andrej Karpathy — Simplicity First / Surgical Changes
- William of Ockham — the razor cuts entities, not requirements; count concepts, not lines
- ML ablation studies — remove one thing, hold the rest constant, measure before you decide
- [HERO-Anti-OverDefense](https://github.com/wanshuiyin/HERO-Anti-OverDefense) — reachable vs constructible
- Joe Armstrong / Erlang — Let it crash (fail the isolated unit; recover outside it)
- LessWrong, pathological guardrailing — fail early and visibly
- [ponytail](https://github.com/DietrichGebert/ponytail) — reuse ladder, ceiling comment, runnable check (this skill rejects its "laziness" philosophy)
- Sandi Metz — The Wrong Abstraction
- Robert C. Martin — Clean Code: abstraction levels, flag-argument tradeoffs, meaningful names, and local cleanup (used as contextual heuristics, not universal deletion rules)
- John Ousterhout — A Philosophy of Software Design
- Rob Pike — Go proverbs
- Martin Fowler — YAGNI's four costs
- Rich Hickey — Simple Made Easy
- Casey Muratori — semantic compression
- Carson Gross — Locality of Behaviour
- [grug](https://grugbrain.dev/) — cut-points
- TigerBeetle TIGER_STYLE & NASA's Power of 10 — where heavy defense is the optimum
- Mitchell Hashimoto — rules as failure log
- OpenClaw AGENTS.md — a fallback is a product decision

## License

[SATA 2.1](./LICENSE.txt) ([source](https://github.com/klarkxy/sata-license)) — The Star And Thank Author License, v2.1: use it freely (MIT terms). Starring and thanking are an invitation, not a condition — you may, arguably you should, but the license holds either way.

