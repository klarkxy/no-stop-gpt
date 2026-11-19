<div align="center">

# No, Stop! GPT!

[![Agent Skill](https://img.shields.io/badge/Agent-Skill-22c55e?style=flat-square)](./skills/no-stop-gpt/SKILL.md)
[![License: SATA 2.1 (modified)](https://img.shields.io/badge/License-SATA%202.1%20%28modified%29-0f172a?style=flat-square)](./LICENSE.txt)

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
agent's skill directory. Keep `SKILL.md`, `references/`, `agents/`, and `LICENSE.txt`
together. To upgrade, replace that entire installed directory with the new copy
to clear out retired reference files. Preserve any local customizations first.

## Use

Invoke `no-stop-gpt` explicitly, or let a host that supports automatic discovery
select it for complexity reviews, scoped cleanup, legacy-code removal,
implementation choices (reuse, added layers, state, or defenses), or excessive
interface copy. Installation does not guarantee activation.

| Mode | Example request | Result |
| --- | --- | --- |
| **Prevent** | “Implement this feature using no-stop-gpt.” | Search for existing solutions first and apply the principles while completing the code change. |
| **Audit** | “Review this diff for overengineering.” | Read-only keep / remove / downgrade / decide findings with evidence. |
| **Sweep** | “Simplify this subsystem and remove unnecessary code.” | Cut obsolete paths, repair dependencies, chase newly dead code, and verify the surviving behavior. |
| **Less is more** | “Use no-stop-gpt: Less is more. Reduce the text on this screen.” | Remove redundant UI copy while preserving clear actions and essential information. |

A request to audit or propose repository cleanup stays read-only. An explicit
request to implement, simplify, or remove authorizes changes within that scope
without another approval of intermediate findings. Within that scope, take the
next authorized step in the same turn rather than handing back a plan, a routine
question, an announcement, or manual steps; apply and verify mid-task
corrections immediately; and deliver a self-contained result. Retiring supported
behavior or compatibility needs direction unless already authorized; publication
and deployment remain separate actions.

Search the codebase, dependencies, and platform or external solutions before
building general-purpose capabilities from scratch, and prefer fewer concepts
among designs that satisfy the same requirements. Preserve necessary security,
data integrity, accessibility, compatibility, and lifecycle guarantees. In
read-only reviews, search counts and passing tests alone do not prove removal
is safe, and finding nothing justified to remove is a valid result; during
authorized cleanup, prefer reversible local cuts over preserving code on
speculation, while keeping non-local obligations protected until verified.

Start with [SKILL.md](./skills/no-stop-gpt/SKILL.md). It routes on demand to
design heuristics, antipatterns, defense and lifecycle guidance, the Sweep
workflow, legacy deletion recipes, and frontend copy rules. Pure explanation,
formatting, and unrelated prose do not trigger the skill.

The skill builds on OpenAI's [Rethinking skills and prompts for GPT-6 Astra](https://developers.openai.com/blog/rethinking-skills-and-prompts-for-gpt-6-astra)
(concise triggers, on-demand detail, decision criteria instead of fixed
itineraries) and adapts task completion and self-contained delivery from
[Using GPT-6.2](https://developers.openai.com/api/docs/guides/latest-model)
(see [adoption boundaries](./skills/no-stop-gpt/references/sources.md#task-completion-and-self-contained-delivery)).
External docs add no authority, vendor parameters, or duty to continue without
end; contract and protection rules apply across all supported agents.

## Aggressive legacy cleanup

Delete obsolete paths, use failures to locate remaining dependencies, and repair
the surviving design. Do not wait for exhaustive proof before a recoverable
local cut or restore a whole old subsystem because one check fails. Migrate
callers, remove tests that only pin retired implementation, and preserve unique
behavior checks. Keep following newly unused helpers, types, configuration, and
dependencies until the requested cleanup is complete.

The [deletion guide](./skills/no-stop-gpt/references/legacy-cleanup.md) gives
concrete recipes for dead files, stale flags, compatibility wrappers, duplicate
implementations, tests, and build artifacts, plus an example of repairing a cut
without recreating the old design (see
[legacy-deletion sources](./skills/no-stop-gpt/references/sources.md#legacy-code-deletion)).

## Less is more: frontend copy

Minimize the text users must read to finish their task. Delete redundant subtitles,
explanatory cards, repeated hints, and implementation commentary. Let clear labels,
controls, and feedback carry the interaction; show secondary help when relevant.
Preserve necessary labels, consequential conditions, errors, and recovery steps.
Fewer words should reduce reading effort without making the interface harder to use.

The [frontend guidance](./skills/no-stop-gpt/references/less-is-more.md) includes
before/after examples. The [research notes](./skills/no-stop-gpt/references/sources.md#interface-copy-and-model-verbosity)
separate usability evidence and measured verbosity bias from hypotheses about
why a particular generated interface is too wordy.

## Related tools and adopted ideas

Compared against upstream instructions on 2026-09-08. These are design references,
not dependencies or a measured ranking; the guidance here is written for this skill.

| Reference | Adopted here | Adaptation |
| --- | --- | --- |
| [Karpathy Guidelines](https://github.com/forrestchang/andrej-karpathy-skills/blob/main/skills/karpathy-guidelines/SKILL.md) | Observable success criteria and local conventions. | Keep useful single-use boundaries; clarify only material choices. |
| [Anthropic code-simplifier](https://github.com/anthropics/claude-plugins-official/blob/main/plugins/code-simplifier/agents/code-simplifier.md) | Readability and ease of debugging when simplifying control flow. | Follow the target project's language and style conventions. |
| [gtapps/simplify](https://github.com/gtapps/simplify/blob/main/SKILL.md) | Concrete efficiency checks, semantic comparison, and reconciliation of overlapping findings. | No mandatory agent team; recheck stale findings and honor intentional behavior changes. |
| [HERO-Anti-OverDefense](https://github.com/wanshuiyin/HERO-Anti-OverDefense/blob/main/RULES.md) | Already covered: reachable cases and checks that inform a decision. | Establish actual trust boundaries and preserve necessary protections. |

Core rules and success criteria live in `SKILL.md`; conditional heuristics,
workflows, examples, and recipes live in `references/`. No additional runtime is
required.

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

## Maintaining the skill

Keep only instructions that change decisions for this workflow. Revisit old
workarounds as models and tools improve; remove obsolete scaffolding and repeated
generic advice. State each shared principle once in `SKILL.md`; references hold
only their own conditional detail. Preserve real constraints for all supported
agents without a model-specific recipe.

## License

[SATA 2.1 (modified)](./LICENSE.txt), based on the [original SATA license](https://github.com/zTrix/sata-license).
