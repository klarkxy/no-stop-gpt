<div align="center">

# No. Stop. GPT.

**反过度设计、反过度防御的 Agent Skill 套件 — 最优化，而非最小化。**

*Anti-over-engineering & anti-over-defensive skills for coding agents — optimize, don't minimize.*

[![Agent Skill](https://img.shields.io/badge/Agent-Skill-22c55e?style=flat-square)](./skills/no-stop-gpt/SKILL.md)
[![License: SATA 2.1](https://img.shields.io/badge/License-SATA%202.1-0f172a?style=flat-square)](./LICENSE.txt)

</div>

编码模型被训练成优化"看起来完整且不炸"：测试保持绿色、没人被责怪。于是它们吞异常、给不可能失败的调用加 fallback、为单一实现建 factory、保留没人要求的兼容层，并把自己上一轮生成的脚手架当成"现在必须保留"的证据。

这个套件让 agent 把复杂度预算花在被要求的事情上：**复杂度与需求和真实信任边界成比例，失败要早且可见。** 它不是"能少写就少写"的懒惰规则——被要求的硬活就是工作本身，账本内核里的重断言就是最优解。目标永远是资深工程师愿意辩护的设计，而不是最短的 diff。

## 套件内容

| Skill | 职责 | 模式 |
|---|---|---|
| [`no-stop-gpt`](./skills/no-stop-gpt/SKILL.md) | 写码时预防过度设计；裁决单个防御机制的去留 | **Prevent**（默认写码护栏）/ **Audit**（只读判定表） |
| [`simplify-codebase`](./skills/simplify-codebase/SKILL.md) | 存量代码库的证据式简化与熵回收 | **Survey**（只读勘察）/ **Change**（授权删改） |

两者互补成闭环：`no-stop-gpt` 管住新增代码、裁决"这个 guard/retry/fallback 还要不要"；`simplify-codebase` 做全库范围的死代码、重复状态、无主抽象清理。全库扫描中遇到单个防御机制的存废问题，会交给 `no-stop-gpt` 的 focused defensive audit。

### no-stop-gpt 的核心：五问决策测试

1. **信任边界** — 数据刚跨过用户/网络/磁盘/第三方边界？是 → 校验；否 → 信任调用方。
2. **可达性** — 本项目支持的用法真能产生这个情况？可达才算数，"原则上可构造"不算。
3. **失败语义** — 调用方能区分失败与成功？不能 → 抛错，禁止同型默认值（`0`、`""`、`'free'`）。
4. **具名消费者** — 这个 hash/flag/gate 改变谁的实时决策？没有 → 不建。
5. **"这个检查触发了我会做什么不同的事？"** — 答不上来 → 不要跑这个检查。

外加硬例外清单（边界校验、authn/authz、数据防丢失、正确性关键系统的生产断言等永不裁剪）与领域画像（账本/存储内核/航天控制里重防御才是最优）。

## 安装

把 `skills/` 下需要的 skill 目录复制到你的 agent skill 目录：

```powershell
# OpenAI Codex
Copy-Item -Recurse skills/no-stop-gpt ~/.codex/skills/
# Claude Code
Copy-Item -Recurse skills/no-stop-gpt ~/.claude/skills/
# Cursor（个人级）
Copy-Item -Recurse skills/no-stop-gpt ~/.cursor/skills/
# 通用（多 agent 共享，Codex/Cursor 均可读取）
Copy-Item -Recurse skills/no-stop-gpt ~/.agents/skills/
```

`simplify-codebase` 同理。项目级安装则复制到仓库的 `.cursor/skills/`、`.claude/skills/` 等目录。

## 使用

- 写码时自动生效（Prevent 模式），或显式唤起：说 `no-stop-gpt`、`anti-overengineering`、`反过度设计`、`过度防御`。
- 审查 diff：*"用 no-stop-gpt 审查这个 PR 的过度设计"* → 输出每个机制的 keep/remove/downgrade 判定表（只读）。
- 裁决单个机制：*"这个 retry 还需要吗？"* → focused defensive audit。
- 全库清理：说 `简化代码库`、`熵回收` → 触发 `simplify-codebase`。

## 思想来源

条款综合改写自 2025–2026 社区实践与经典文献，致谢（按出现顺序不分先后）：

Andrej Karpathy（Simplicity First / Surgical Changes）· [HERO-Anti-OverDefense](https://github.com/wanshuiyin/HERO-Anti-OverDefense)（reachable vs constructible）· LessWrong pathological guardrailing（fail early and visibly）· [ponytail](https://github.com/DietrichGebert/ponytail)（reuse ladder、ceiling comment、runnable check——但本套件反其"懒惰"哲学而行）· Sandi Metz（The Wrong Abstraction）· John Ousterhout（A Philosophy of Software Design）· Rob Pike（Go proverbs）· Martin Fowler（YAGNI 四成本）· Rich Hickey（Simple Made Easy）· Casey Muratori（semantic compression）· Carson Gross（Locality of Behaviour）· [grug](https://grugbrain.dev/)（cut-points）· TigerBeetle TIGER_STYLE 与 NASA Power of 10（重防御的领域边界）· Mitchell Hashimoto（rules as failure log）· OpenClaw AGENTS.md（fallback 是产品决策）。

---

## English

Coding models are trained to optimize for *looking complete without exploding*: tests stay green, nobody gets blamed. So they swallow exceptions, add fallbacks to calls that cannot fail, build factories for single implementations, keep compatibility layers nobody asked for, and treat their own earlier scaffolding as proof a capability is required.

This suite makes the agent spend its complexity budget on the requested work: **complexity proportional to the request and the real trust boundary; failures early and visible.** It is explicitly *not* a "write less code" laziness ruleset — requested hard work is the work, and heavy assertions in a ledger kernel are the optimum. The target is always the design a senior engineer would defend, never the smallest diff.

**`no-stop-gpt`** — in-change prevention (Prevent mode) plus a focused defensive audit that emits keep / remove / downgrade verdicts for individual guards, retries, fallbacks, and shims (Audit mode, read-only). Core: five decision tests (trust boundary, reachability, failure semantics, named consumer, "what would I do differently?"), hard exceptions that are never traded away, and a domain portrait for systems where heavy defense *is* the optimum.

**`simplify-codebase`** — evidence-backed simplification of existing codebases: dead code, duplicate state, redundant layers, ownerless abstractions. Survey (read-only) and Change (authorized) modes. Hands single-mechanism calls to `no-stop-gpt`.

**Install:** copy the skill folders from `skills/` into `~/.codex/skills/`, `~/.claude/skills/`, `~/.cursor/skills/`, or the shared `~/.agents/skills/`.

**Trigger:** applies automatically while coding, or say `no-stop-gpt` / `anti-overengineering`; say "simplify the codebase" for the repo-wide sweep.

## License

[SATA 2.1](./LICENSE.txt)（[license source](https://github.com/klarkxy/sata-license)）— The Star And Thank Author License, v2.1: use freely (MIT terms). Starring and thanking are an invitation, not a condition — you may, and arguably you should, but the license holds either way. 用得开心的话，最好点个 star、谢谢作者；可以点，应该点，但不强制，不点也照样授权。
