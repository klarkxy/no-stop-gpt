<div align="center">

# No, Stop! GPT!

[![Agent Skill](https://img.shields.io/badge/Agent-Skill-22c55e?style=flat-square)](./skills/no-stop-gpt/SKILL.md)
[![License: SATA 2.0 (modified)](https://img.shields.io/badge/License-SATA%202.0%20%28modified%29-0f172a?style=flat-square)](./LICENSE.txt)

[English](./README.md)

<img src="./assets/mascot.png" alt="No, Stop! GPT! 看板娘——过度设计被抓现行的 GPT 娘" width="240">

</div>

让编程智能体少加无用抽象、重复状态和隐藏失败的兜底路径，以合适的复杂度完成全部需求。

## 安装

使用 [skills CLI](https://github.com/vercel-labs/skills)：

```bash
npx skills add klarkxy/no-stop-gpt
```

也可以把整个 `skills/no-stop-gpt/` 目录复制到所用智能体的技能目录中，保留其中的 `SKILL.md`、`references/`、`agents/` 和 `LICENSE.txt`。升级时用新目录整体替换旧目录，避免留下已退役的参考文件；先保存自己的本地定制。

## 使用

可以显式调用 `no-stop-gpt`，也可由支持自动发现的宿主在复杂度审查、指定范围的清理，或涉及新增层次、状态、防御机制的实现选择中启用它。安装不代表每次都会启用。

| 模式 | 请求示例 | 结果 |
| --- | --- | --- |
| **Prevent** | “用 no-stop-gpt 实现这个功能。” | 在完成代码修改时应用原则。 |
| **Audit** | “审查这个 diff 的过度设计。” | 只读输出保留、移除、简化或待定结论，并附证据。 |
| **Sweep** | “精简这个子系统，移除不需要的代码。” | 调查、实施有依据的清理，并验证保留的行为。 |

审查或提出仓库清理建议的请求保持只读。明确的清理请求授权范围内的修改，不需要再审批中间发现。撤回受支持的行为或兼容性，若尚未授权，需要用户决定；发布和部署另行授权。

在满足同一需求的方案中，优先选择概念更少的设计。保留必要的安全、数据完整性、可访问性、兼容性和生命周期保证。搜索命中数与测试通过不能单独证明删除安全；没有合理删除项也是有效结果。

从 [SKILL.md](./skills/no-stop-gpt/SKILL.md) 开始，按需读取设计案例、防御与生命周期指引，以及合并后的 Sweep 流程。纯解释、格式调整和无关文字修改不触发技能。

技能参考 OpenAI 的 [Rethinking skills and prompts for GPT-6 Astra](https://developers.openai.com/blog/rethinking-skills-and-prompts-for-gpt-6-astra)：缩短触发描述，按当前问题加载细节，用决策依据替代固定步骤。已授权的实现持续推进到相关验证和问题修复完成，中间发现无需再次审批；契约和保护要求仍适用于所有支持的智能体。

## 同类工具与借鉴

2026-09-08 对照上游指令。这些是设计参考，不是运行依赖或实测排名；本项目按自身用途重新编写规则。

| 参考项目 | 吸收的做法 | 本项目的取舍 |
| --- | --- | --- |
| [Karpathy Guidelines](https://github.com/forrestchang/andrej-karpathy-skills/blob/main/skills/karpathy-guidelines/SKILL.md) | 先明确可观察的成功条件，遵循局部约定。 | 保留有价值的单次使用边界，只为实质性分歧澄清。 |
| [Anthropic code-simplifier](https://github.com/anthropics/claude-plugins-official/blob/main/plugins/code-simplifier/agents/code-simplifier.md) | 简化控制流时兼顾可读性和调试便利。 | 语言和风格遵循目标项目自身约定。 |
| [gtapps/simplify](https://github.com/gtapps/simplify/blob/main/SKILL.md) | 具体的效率检查、语义比较，以及重叠审查结论的协调。 | 不强制智能体团队；重新核对过时结论，尊重有意的行为变更。 |
| [HERO-Anti-OverDefense](https://github.com/wanshuiyin/HERO-Anti-OverDefense/blob/main/RULES.md) | 已覆盖：按实际可达情况判断，让检查服务于决策。 | 核实真实信任边界，保留必要保护。 |

成功条件放在技能入口；条件性例子归入已有的设计、反模式和 Sweep 参考文档，无需新增运行组件。

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
- Robert C. Martin — Clean Code《代码整洁之道》：抽象层次、旗标参数的取舍、清楚命名和局部清理（作为结合语境的启发，不作为通用删除规则）
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

[SATA 2.0（本项目修改版）](./LICENSE.txt)，基于[原版 SATA 许可证](https://github.com/zTrix/sata-license)。给项目点个 Star，再谢谢作者——当然，只在心里感谢也可以。本项目版本仅补充明确：Star 与致谢均为自愿。

