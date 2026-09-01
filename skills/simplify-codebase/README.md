<div align="center">

# Simplify Codebase

**先证明，再删除。让代码库少维护一些事实、状态与契约。**

[![Agent Skill](https://img.shields.io/badge/Agent-Skill-22c55e?style=flat-square)](./SKILL.md)
[![License: SATA](https://img.shields.io/badge/License-SATA%202.0-f59e0b?style=flat-square)](../../LICENSE.txt)
[![中文](https://img.shields.io/badge/README-中文-06b6d4?style=flat-square)](./README.md)
[![English](https://img.shields.io/badge/README-English-64748b?style=flat-square)](./README.en.md)

<img src="./assets/hero.png" alt="复杂的软件结构经过证据验证后，收敛为更小、更清晰的系统" width="100%" />

</div>

`simplify-codebase` 是一个面向现有代码库的 Agent Skill。它帮助编码智能体识别并安全移除偶然复杂度，同时保护仍然有效的行为、边界与兼容性。

它不追求“删得多”。它关心的是：一次改动能否减少团队今后必须持续保持一致的概念和义务。

## 为什么需要它

代码库里的冗余很少只是“某个函数没人调用”。它也可能是重复状态、失去所有者的抽象、只剩测试消费的接口、早已无效的兼容路径，或者被保留在共享文件中的半截功能。

静态检查可以提供线索，但不能单独证明一项删除是安全的。这个 Skill 会继续追踪运行时消费者、动态注册、持久化格式、公共接口、历史决策与验证边界，再决定应该删除、合并、保留，还是标记为暂时无法判断。

> **核心原则：** 删除代码行只是结果。真正的收益是删除一个需要长期维护的事实、状态、契约或概念。

## 工作方式

| | 聚焦范围 `Focused` | 全库范围 `Broad` |
| --- | --- | --- |
| **只读审计 `Survey`** | 深挖一个子系统、状态机或疑似重复点 | 分区覆盖整个仓库，给出候选、反证与盲区 |
| **授权修改 `Change`** | 证明并完成一个明确的简化边界 | 按所有权边界分批修改，每批独立验证 |

每个候选都要形成一份证明记录：

- 它增加了什么维护负担；
- 生产、测试、动态和外部消费者分别是谁；
- 完整删除边界在哪里，包括共享文件内部的成员；
- 会放弃什么可观察行为或兼容性；
- 哪个最小验证可以揭示错误删除；
- 减少的复杂度是否大于新引入的迁移或替代机制。

## 它会谨慎对待

- 公共 API、动态加载和插件注册；
- 数据格式、迁移、回放与向后兼容；
- 授权、隔离、输入校验和数据丢失防护；
- 并发、取消、清理和生命周期所有权；
- 生成文件、共享资源和仓库外消费者；
- 仍然有效的 ADR、RFC 与架构约束。

发现真实消费者、边界尚未查清，或者简化只是把复杂度搬到别处时，它会建议保留，而不是为了输出结果强行删除。

## 安装

让 Codex 安装：

```text
Install the simplify-codebase skill from https://github.com/tt-a1i/simplify-codebase
```

也可以手动安装到 Codex 的用户级 Skill 目录：

```bash
git clone https://github.com/tt-a1i/simplify-codebase.git \
  ~/.codex/skills/simplify-codebase
```

安装后请新建一个任务，让 Skill 目录重新加载。其他支持 `SKILL.md` 的 Agent 环境可将本仓库放入各自的 Skill 目录。

## 使用

### 审计整个仓库，不改文件

```text
使用 $simplify-codebase 审计这个仓库，列出最安全、收益最高的简化候选。不要修改文件。
```

### 调查一个具体问题

```text
使用 $simplify-codebase 判断这些 readiness 标志是在表达不同的生命周期保证，还是重复状态。
```

### 实施一个已经证明安全的简化

```text
使用 $simplify-codebase 删除一个高置信度的偶然复杂度来源。保留仍然有效的契约，完成验证，并给出操作回执和撤销路径。
```

### 合并其他分支或 Agent 的发现

```text
使用 $simplify-codebase 复核并整合这个 PR 中的简化建议。保留证据，不保留候选数量。
```

## 输出是什么样的

只读审计会交付覆盖范围、排序后的证明记录、重要反例、未决问题和下一条所需证据。

修改任务会额外交付实际变更、分层验证结果、剩余风险、操作回执与可执行的撤销路径。一次小范围测试通过，不会被包装成完整的运行时或用户验收。

## 仓库结构

```text
.
├── SKILL.md                    # 主工作流与判断标准
├── agents/openai.yaml          # Agent 展示与调用元数据
├── references/
│   ├── investigation.md        # 全库调查与候选发现
│   ├── boundaries-and-lifecycle.md
│   ├── execution-and-recovery.md
│   ├── decision-records.md
│   └── integrating-findings.md
├── docs/validation.md          # 行为验证与质量证据
└── assets/hero.png             # 原创 Hero 视觉
```

## 质量与边界

这个版本经过 Change、Broad、Integration 和 Decision-record 场景验证，也在一个 973 文件的 Python + TypeScript 项目上完成过全库审计。测试方法与已知边界记录在 [docs/validation.md](./docs/validation.md)。

Skill 不能替代产品决策。删除仍然可达的能力、已支持接口、持久化表示或兼容路径时，仍需由使用者明确授权。

## 贡献

欢迎提交 Issue 和 PR。请优先提供失败案例、遗漏的消费者、错误删除风险或可以复现的验证缺口；这比单纯增加更多规则更有价值。

## License

[SATA 2.0](../../LICENSE.txt)
