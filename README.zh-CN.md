<div align="center">

# No, Stop! GPT!

[![Agent Skill](https://img.shields.io/badge/Agent-Skill-22c55e?style=flat-square)](./skills/no-stop-gpt/SKILL.md)
[![License: SATA 2.1 (modified)](https://img.shields.io/badge/License-SATA%202.1%20%28modified%29-0f172a?style=flat-square)](./LICENSE.txt)

[English](./README.md)

<img src="./assets/mascot.png" alt="No, Stop! GPT! 看板娘——过度设计被抓现行的 GPT 娘" width="240">

</div>

让编程智能体少加无用抽象、重复状态和隐藏失败的兜底路径，以合适的复杂度完成全部需求。

## 安装

使用 [skills CLI](https://github.com/vercel-labs/skills)：

```bash
npx skills add klarkxy/no-stop-gpt
```

也可以把整个 `skills/no-stop-gpt/` 目录复制到所用智能体的技能目录中，保留其中的 `SKILL.md`、`references/`、`agents/` 和 `LICENSE.txt`。升级时用新目录整体替换旧目录，清除已退役的参考文件；先保存自己的本地定制。

## 使用

可以显式调用 `no-stop-gpt`，也可由支持自动发现的宿主在复杂度审查、指定范围清理、旧代码清除、涉及复用、新增层次、状态或防御机制的实现选择，以及界面文字过多时启用它。安装不代表每次都会启用。

| 模式 | 请求示例 | 结果 |
| --- | --- | --- |
| **Prevent** | “用 no-stop-gpt 实现这个功能。” | 先检索已有方案再动手，在完成代码修改时应用原则。 |
| **Audit** | “审查这个 diff 的过度设计。” | 只读输出保留、移除、简化或待定结论，并附证据。 |
| **Sweep** | “精简这个子系统，移除不需要的代码。” | 删除旧路径、修复依赖、追删新暴露的死代码，并验证保留的行为。 |
| **Less is more** | “用 no-stop-gpt 的 Less is more，精简这个页面的文字。” | 删除冗余界面文案，保留清楚的操作和必要信息。 |

审查或提出仓库清理建议的请求保持只读。明确的实现、精简或删除请求授权范围内的修改，不需要再审批中间发现。在授权范围内，于同一轮直接执行下一步，不以计划、例行提问、下一步预告或交回用户执行的步骤收尾；对任务中的纠偏立即修改并验证，最终交付可以独立阅读的结果。撤回受支持的行为或兼容性，若尚未授权，需要用户决定；发布和部署另行授权。

实现通用能力前先检索仓库、现有依赖、平台能力或成熟外部方案；在满足同一需求的方案中，优先选择概念更少的设计。保留必要的安全、数据完整性、可访问性、兼容性和生命周期保证。在只读审查中，搜索命中数与测试通过不能单独证明删除安全，没有合理删除项也是有效结果；在已授权的清理中，对局部可验证路径优先尝试可恢复的删除而非因猜测保留，对无法在本地验证的外部或契约义务则先确认再删。

从 [SKILL.md](./skills/no-stop-gpt/SKILL.md) 开始，按需路由到设计取舍、反模式目录、防御与生命周期指引、Sweep 流程、旧代码清理指南及前端文案规则。纯解释、格式调整和无关文字修改不触发技能。

技能结构参考 OpenAI 的 [Rethinking skills and prompts for GPT-6 Astra](https://developers.openai.com/blog/rethinking-skills-and-prompts-for-gpt-6-astra)（精简触发描述、按需加载细节、用决策依据替代固定步骤），并吸收了 [Using GPT-6.2](https://developers.openai.com/api/docs/guides/latest-model) 的任务完成与自包含交付原则（见[采用边界](./skills/no-stop-gpt/references/sources.md#task-completion-and-self-contained-delivery)）。外部文档不增加授权、不引入供应商 API 参数，也不要求工作永不停止；契约与保护要求仍适用于所有支持的智能体。

## 激进清理旧代码

删除旧路径，用失败定位剩余依赖，再修通保留的设计。对局部可恢复的删除无需等待穷尽证明，也不要因为一次检查失败就恢复整个旧子系统。迁移调用者，删除只绑定已退役实现的测试，保留能检测独有行为的检查。继续追删失去用途的辅助函数、类型、配置和依赖，直到完成约定范围。

[删除指南](./skills/no-stop-gpt/references/legacy-cleanup.md)提供死文件、旧开关、兼容包装、重复实现、测试和构建产物的具体配方，并演示如何修复删除后的问题而不重建旧设计（参考来源见[旧代码清理资料](./skills/no-stop-gpt/references/sources.md#legacy-code-deletion)）。

## Less is more：前端文案

尽量减少用户完成当前任务必须阅读的文字。删除重复副标题、解释卡片、反复提示和实现说明，让清楚的标签、控件和反馈承担交互表达；次要帮助在需要时出现。保留必要标签、影响决定的条件、错误和恢复方法。以阅读负担降低且操作仍然清楚为准。

[前端指引](./skills/no-stop-gpt/references/less-is-more.md)包含精简前后的例子。[研究记录](./skills/no-stop-gpt/references/sources.md#interface-copy-and-model-verbosity)区分了可用性依据、实验发现的模型冗长偏差，以及对具体界面为什么文字过多的推测。

## 同类工具与借鉴

2026-09-08 对照上游指令。这些是设计参考，不是运行依赖或实测排名；本项目按自身用途重新编写规则。

| 参考项目 | 吸收的做法 | 本项目的取舍 |
| --- | --- | --- |
| [Karpathy Guidelines](https://github.com/forrestchang/andrej-karpathy-skills/blob/main/skills/karpathy-guidelines/SKILL.md) | 先明确可观察的成功条件，遵循局部约定。 | 保留有价值的单次使用边界，只为实质性分歧澄清。 |
| [Anthropic code-simplifier](https://github.com/anthropics/claude-plugins-official/blob/main/plugins/code-simplifier/agents/code-simplifier.md) | 简化控制流时兼顾可读性和调试便利。 | 语言和风格遵循目标项目自身约定。 |
| [gtapps/simplify](https://github.com/gtapps/simplify/blob/main/SKILL.md) | 具体的效率检查、语义比较，以及重叠审查结论的协调。 | 不强制智能体团队；重新核对过时结论，尊重有意的行为变更。 |
| [HERO-Anti-OverDefense](https://github.com/wanshuiyin/HERO-Anti-OverDefense/blob/main/RULES.md) | 已覆盖：按实际可达情况判断，让检查服务于决策。 | 核实真实信任边界，保留必要保护。 |

核心规则与成功条件放在入口 `SKILL.md`；条件性取舍、流程、案例和清理配方按需放入 `references/`，无需新增运行组件。

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

## 维护技能

只保留会改变这一工作流决策的指令。随着模型和工具进步，重新审视旧的变通做法，删除过时的脚手架和重复的通用建议。共享原则只在 `SKILL.md` 中说一次，参考文档只放各自的条件性细节。保留对所有支持的智能体都成立的真实约束，不写针对特定模型的配方。

## 许可证

[SATA 2.1（本项目修改版）](./LICENSE.txt)，基于[原版 SATA 许可证](https://github.com/zTrix/sata-license)。
