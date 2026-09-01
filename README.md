<div align="center">

# No, Stop! GPT!

[![Agent Skill](https://img.shields.io/badge/Agent-Skill-22c55e?style=flat-square)](./skills/no-stop-gpt/SKILL.md)
[![License: SATA 2.1](https://img.shields.io/badge/License-SATA%202.1-0f172a?style=flat-square)](./LICENSE.txt)

[简体中文](./README.zh-CN.md)

<img src="./assets/mascot.png" alt="No, Stop! GPT! mascot — GPT-chan caught mid-over-engineering" width="240">

</div>

Coding agents love doing more than you asked. A one-line change grows a factory. A call that can't fail gets three layers of try-catch and a fallback. Scaffolding the agent wrote last turn becomes "proof" the capability is required. They're not broken — they're trained to look complete and never crash. Your codebase pays for it.

This skill is the hand in the picture. It keeps complexity proportional to the request and the real trust boundary, and when something goes wrong it fails early and loudly, at the unit that owns the work. It's not a license to slack off: requested hard work is the work, and heavy assertions in a ledger kernel are the optimum. The goal is the design a senior engineer would defend — not the smallest diff.

## Three modes

| Mode | What it does | Permissions |
|---|---|---|
| **Prevent** | Default rails while writing code: change discipline + five decision tests, so over-design never lands | Always on |
| **Audit** | Judges one defensive mechanism (guard / retry / fallback / shim): keep / remove / downgrade, with evidence | Read-only; deleting needs your go-ahead |
| **Sweep** | Repo-wide entropy cleanup: dead code, duplicate state, redundant layers, ownerless abstractions — prove first, then delete | Survey (read-only) / Change (authorized) |

When a Sweep hits a single mechanism it's unsure about, it runs a focused Audit on the spot.

### The five decision tests

Before any mechanism gets added — or kept:

1. **Trust boundary** — did this data just cross a user / network / disk / third-party boundary? Validate if it did; trust the caller if it didn't.
2. **Reachability** — can this project's real usage actually produce this case? "Constructible in principle" doesn't count.
3. **Failure semantics** — can the caller tell failure from success? If not, throw out of the unit that owns the work; a same-shaped default (`0`, `""`, `'free'`) is a lie.
4. **Named consumer** — name someone whose live decision changes because of this hash / flag / gate. Can't? Don't build it.
5. **"What would I do differently if this fired?"** — no answer, no check.

Hard exceptions — boundary validation, authn/authz, data-loss prevention, … — are never on the table. Full ruleset: [SKILL.md](./skills/no-stop-gpt/SKILL.md).

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

- It's already active while you code (Prevent). Or call it by name: `no-stop-gpt`, `anti-overengineering`, `反过度设计`.
- Hand it a diff or PR → a keep / remove / downgrade verdict per mechanism. Read-only; it won't touch anything.
- Ask *"is this retry still necessary?"* → a focused defensive audit.
- Say *"simplify the codebase"* → Sweep: a read-only survey with ranked evidence first, deletion only after you authorize it.

Sweep's behavioral validation: [docs/sweep-validation.md](./docs/sweep-validation.md).

## Credits

The rules are adapted from 2025–2026 community practice and classic texts. Thanks, in no particular order:

- Andrej Karpathy — Simplicity First / Surgical Changes
- [HERO-Anti-OverDefense](https://github.com/wanshuiyin/HERO-Anti-OverDefense) — reachable vs constructible
- Joe Armstrong / Erlang — Let it crash (fail the isolated unit; recover outside it)
- LessWrong, pathological guardrailing — fail early and visibly
- [ponytail](https://github.com/DietrichGebert/ponytail) — reuse ladder, ceiling comment, runnable check (this skill rejects its "laziness" philosophy)
- Sandi Metz — The Wrong Abstraction
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
