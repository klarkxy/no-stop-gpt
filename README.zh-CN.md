<div align="center">

# No, Stop! GPT!

[![Agent Skill](https://img.shields.io/badge/Agent-Skill-22c55e?style=flat-square)](./skills/no-stop-gpt/SKILL.md)
[![License: SATA 2.1](https://img.shields.io/badge/License-SATA%202.1-0f172a?style=flat-square)](./LICENSE.txt)

[English](./README.md)

<img src="./assets/mascot.png" alt="No, Stop! GPT! 看板娘——过度设计被抓现行的 GPT 娘" width="240">

</div>

写代码的 AI 都有个职业病：爱多做一点。一行能改完的事，它给你建个 factory；不可能失败的调用，它包三层 try-catch 再塞个 fallback；上一轮它自己生成的脚手架，下一轮就成了“这功能果然有必要”的证据。

也不是它坏——模型的训练目标就是“看起来完整、千万别炸”。测试是绿的，就没人挨骂，代价是你的代码越来越肿。

这个 skill 就是图里那只手。它让复杂度始终配得上需求和真实的信任边界，出了问题就早点炸、大声炸，炸在拥有这项工作的那一层。但它不是教 AI 偷懒：该干的硬活一件不能少，账本内核里的重断言就是最优解。目标永远是“资深工程师愿意辩护的设计”，而不是“最短的 diff”。

## 三个模式

| 模式 | 干什么 | 权限 |
|---|---|---|
| **Prevent** | 写代码时的默认护栏：变更纪律 + 五问决策测试，过度设计从源头就不进来 | 默认生效 |
| **Audit** | 裁决单个防御机制（guard / retry / fallback / 兼容层）：keep / remove / downgrade / decide，附证据 | 只读；删东西要你点头 |
| **Sweep** | 全库熵回收：死代码、重复状态、冗余层、无主抽象——先证明，再删除 | Survey 只读勘察 / Change 授权删改 |

Sweep 途中遇到单个机制拿不准，会就地调一次 Audit。

### 五问决策测试

任何机制想进来——或者想留下——先过这五问：

1. **信任边界** — 数据刚跨过用户 / 网络 / 磁盘 / 第三方边界？跨了就校验，没跨就信任调用方。
2. **可达性** — 这个项目真实的使用方式，能产生这种情况吗？“理论上构造得出来”不算数。
3. **失败语义** — 调用方分得清失败和成功吗？分不清就往外抛，让拥有这项工作的单元失败——`0`、`""`、`'free'` 这种同型默认值是撒谎。
4. **具名消费者** — 说出一个会因为这个 hash / flag / gate 改变决策的人。说不出，就别建。
5. **“它真触发了，我会做什么不一样的事？”** — 答不上来，这个检查就别跑。

五问背后有两件工具。**奥卡姆剃刀**在满足同一契约的设计之间做选择：实体最少者胜——类型、状态、层、flag、依赖——它剃的是实体，从来不剃需求。**消融试验**判定已经存在的东西是否真在承重：拿掉它，跑一个真能失败的检查，看结果。测试套件因为从没碰到过这个机制而保持绿色，那是沉默，不是证据——所以单凭消融永远删不掉任何硬例外。

硬例外——边界校验、authn/authz、数据防丢失……——永远不在谈判桌上。完整规则在 [SKILL.md](./skills/no-stop-gpt/SKILL.md)。

## 安装

有开放的 [skills CLI](https://github.com/vercel-labs/skills) 的话，一条命令：

```bash
npx skills add klarkxy/no-stop-gpt
```

或者手动把 `skills/no-stop-gpt/` 复制到 agent 的 skill 目录：

```powershell
# OpenAI Codex
Copy-Item -Recurse skills/no-stop-gpt ~/.codex/skills/
# Claude Code
Copy-Item -Recurse skills/no-stop-gpt ~/.claude/skills/
# Cursor（个人级）
Copy-Item -Recurse skills/no-stop-gpt ~/.cursor/skills/
# 通用（Codex / Cursor / Kimi Code CLI 都认这里）
Copy-Item -Recurse skills/no-stop-gpt ~/.agents/skills/
```

项目级安装就放进仓库的 `.cursor/skills/`、`.claude/skills/` 或 `.agents/skills/`。

## 使用

- 平时写代码它自己就在（Prevent）；也可以喊一声 `no-stop-gpt`、`anti-overengineering` 或 `反过度设计`。
- 丢给它一个 diff 或 PR → 每个机制给一条 keep / remove / downgrade / decide 判定。只读，不动手。`decide` 是指代码和设计文档对“这东西还活着吗”说法不一——这一票归你。
- 问它 *“这个 retry 还有必要吗？”* → 一次 focused defensive audit。
- 说 *“全面审查一下过度防御”* → Sweep 勘察，每个机制一条判定；之后说“你自行决定”，就变成 Sweep 删改：带证明记录，按 ownership 边界逐个验证。
- 说 *“简化一下代码库”* 或 *“熵回收”* → Sweep：先只读勘察，交出带证据的候选清单，你点头之后才删。

Sweep 的行为验证记录在 [docs/sweep-validation.zh-CN.md](./docs/sweep-validation.zh-CN.md)。

## 思想来源

条款改写自 2025–2026 年的社区实践和经典文献。致谢，不分先后：

- Andrej Karpathy — Simplicity First / Surgical Changes
- 奥卡姆（William of Ockham）— 剃实体，不剃需求；数概念，不数行数
- 机器学习消融实验（ablation study）— 每次只拿掉一个，其余保持不变，先测再判
- [HERO-Anti-OverDefense](https://github.com/wanshuiyin/HERO-Anti-OverDefense) — reachable vs constructible
- Joe Armstrong / Erlang — Let it crash（隔离单元内该崩就崩，恢复放在外面）
- LessWrong pathological guardrailing — fail early and visibly
- [ponytail](https://github.com/DietrichGebert/ponytail) — reuse ladder、ceiling comment、runnable check（本套件反其“懒惰”哲学而行）
- Sandi Metz — The Wrong Abstraction
- Robert C. Martin — Clean Code《代码整洁之道》：一个函数一层抽象、旗标参数即两件事、名字就是契约、注释掉的代码直接删（本套件把童子军军规限定在你改过的那几行，并在重复与错误抽象之间选重复）
- John Ousterhout — A Philosophy of Software Design
- Rob Pike — Go proverbs
- Martin Fowler — YAGNI 四成本
- Rich Hickey — Simple Made Easy
- Casey Muratori — semantic compression
- Carson Gross — Locality of Behaviour
- [grug](https://grugbrain.dev/) — cut-points
- TigerBeetle TIGER_STYLE 与 NASA Power of 10 — 重防御就是最优解的领域边界
- Mitchell Hashimoto — rules as failure log
- OpenClaw AGENTS.md — fallback 是产品决策

## 许可证

[SATA 2.1](./LICENSE.txt)（[上游源码](https://github.com/klarkxy/sata-license)）—— Star And Thank Author License v2.1：自由使用（等同 MIT 条款）。Star 与致谢是邀请、不是条件——你可以给，甚至可以说应该给，但给不给，许可证都同样有效。
