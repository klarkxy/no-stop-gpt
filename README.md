<div align="center">

# No, Stop! GPT!

[![Agent Skill](https://img.shields.io/badge/Agent-Skill-22c55e?style=flat-square)](./skills/no-stop-gpt/SKILL.md)
[![License: SATA 2.1](https://img.shields.io/badge/License-SATA%202.1-0f172a?style=flat-square)](./LICENSE.txt)

[简体中文](./README.zh-CN.md)

<img src="./assets/mascot.png" alt="No, Stop! GPT! mascot — GPT-chan caught mid-over-engineering" width="240">

</div>

Keep coding agents from adding unnecessary layers, duplicate state, and fallbacks
that hide failure. Deliver the complete requested behavior with proportional complexity.

## Install

With the [skills CLI](https://github.com/vercel-labs/skills):

```bash
npx skills add klarkxy/no-stop-gpt
```

For manual installation, copy the entire `skills/no-stop-gpt/` directory into your
agent's skill directory. Keep `SKILL.md`, `references/`, and `agents/` together.

## Use

Invoke `no-stop-gpt` explicitly, or let a host that supports automatic discovery
select it for substantive coding work. Installation does not guarantee activation.

| Mode | Example request | Result |
| --- | --- | --- |
| **Prevent** | “Implement this feature using no-stop-gpt.” | Apply the principles while completing the code change. |
| **Audit** | “Review this diff for overengineering.” | Read-only keep / remove / downgrade / decide findings with evidence. |
| **Sweep** | “Simplify this subsystem and remove unnecessary code.” | Investigate, apply justified cuts, and verify the surviving behavior. |

A request to audit or propose repository cleanup stays read-only. An explicit
cleanup request authorizes changes within its scope without another approval of
intermediate findings. Retiring supported behavior or compatibility needs direction
unless already authorized; publication and deployment remain separate actions.

Prefer fewer concepts among designs that satisfy the same requirements. Preserve
necessary security, data integrity, accessibility, compatibility, and lifecycle
guarantees. Search counts and passing tests alone do not prove something is safe
to remove; no justified deletion is a valid result.

Start with [SKILL.md](./skills/no-stop-gpt/SKILL.md). It links to optional design
examples, focused defense and lifecycle guidance, and the consolidated Sweep
workflow. Pure explanation, formatting, and unrelated prose do not trigger the skill.

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

