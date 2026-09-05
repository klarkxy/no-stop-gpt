<div align="center">

# No, Stop! GPT!

[![Agent Skill](https://img.shields.io/badge/Agent-Skill-22c55e?style=flat-square)](./skills/no-stop-gpt/SKILL.md)
[![License: SATA 2.1](https://img.shields.io/badge/License-SATA%202.1-0f172a?style=flat-square)](./LICENSE.txt)

[English](./README.md)

<img src="./assets/mascot.png" alt="No, Stop! GPT! 看板娘——过度设计被抓现行的 GPT 娘" width="240">

</div>

AI 编程有时会把小改动扩成多层抽象、重复状态，或者用 fallback 隐藏失败。这个技能让复杂度与需求和真实边界相称，也防止另一种交付不足：删掉需求、只修表面症状，或把困难工作留到下一轮。

## 三个模式

| 模式 | 用途 | 权限 |
|---|---|---|
| **Prevent** | 实质代码修改中的轻量判断原则 | 沿用编程任务的已有范围 |
| **Audit** | 对机制、diff 或 PR 给出有证据的判断 | 审查请求只读 |
| **Sweep** | 简化子系统或整个仓库 | Survey 只读；明确清理请求授权范围内的修改 |

普通 Prevent 只需读取主文件。设计案例、防御审查和深入清理流程留在参考文档中，按任务需要加载。

### 判断原则

按问题选用相关维度，不要求每行代码都通过一套清单：

- **边界与所有权：** 输入信任、可变状态、生命周期、并发与失败归属。
- **可达性：** 受支持的使用能产生哪些情况，包括动态及外部消费者。
- **失败语义：** 调用方能区分真实失败、合法成功与合法缺失。
- **消费者与目的：** 机制服务的行为或保证是什么。
- **决策价值：** 哪些新证据会改变当前判断。

在满足同一需求的设计中，优先选择概念更少的方案。调用次数和代码行数是线索，不是设计门槛。单次使用的抽象也可能保护必要边界，相似的检查也可能覆盖不同失败窗口。

先依据契约和消费者判断。消融是解决适合实验的疑问时可选的工具；检查通过只支持被覆盖的情形，不能证明没有消费者或删除必然安全。

保留必要的安全、数据完整性、兼容性、可访问性和生命周期保证。只要相同结果仍由明确的所有者保障，冗余实现可以简化。完整指引见 [SKILL.md](./skills/no-stop-gpt/SKILL.md)。
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

- 可以显式调用 `no-stop-gpt`，也可由支持自动发现的宿主在实质编程任务中选择它。安装不代表每次都会启用。
- 说“审查这个 diff 的过度设计”或“这个 retry 还有必要吗”，得到只读结论：**keep**（保留）、**remove**（移除）、**downgrade**（简化实现并保留保证）、**decide**（证据或重要选择未决）。简单问题简短回答，复杂发现使用结构化记录。
- 说“全面审查仓库的过度防御”，进入 Sweep 勘察，覆盖指定范围，并说明保留项与未决问题。
- 说“简化这个子系统”或“应用这些结论”，即授权范围内的调查和修改，不再等待一次中间结论审批。切换模式不会重置已有授权。
- 普通可逆实现由执行者判断；尚未授权的需求、兼容性、权限或不可逆影响变化需要用户决定。发布和部署是另外的动作。

完成标准跟随请求：做完实现或全部已授权的清理范围，在用户约束内使用相关检查。没有新变化或疑问，不重复已通过的检查。只读审查以回答问题为完成；没有合理删除项也是有效结果。

纯解释、格式调整和无关文字修改不触发技能。普通代码涉及安全或存储时仍可使用比例原则，但安全、迁移等专项任务不会被转成清理工作。

历史验证记录见 [docs/sweep-validation.zh-CN.md](./docs/sweep-validation.zh-CN.md)。这些结果针对旧版，不覆盖本次修订；本次未进行结构或行为验证。
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

[SATA 2.1](./LICENSE.txt)（[上游源码](https://github.com/klarkxy/sata-license)）—— Star And Thank Author License v2.1：自由使用（等同 MIT 条款）。Star 与致谢是邀请、不是条件——你可以给，甚至可以说应该给，但给不给，许可证都同样有效。

