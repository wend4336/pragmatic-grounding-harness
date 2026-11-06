# Pragmatic Grounding Harness

本仓库整理了一场关于 **Pragmatic Grounding（语用接地）**、Agent Runtime 与 Harness 架构的讨论，并在此基础上提出一个核心表达：

```text
Agentₚ = RunH(LLM, Purpose, State)
```

Agent 不是 `Model + Harness` 的静态组件拼装，而是由 Purpose 驱动、由 Host 承载、由 State 保持连续性的运行过程。其产物也不只是一个回答，而应包括 **Outcome + Trajectory**。

## 核心命题

- **LLM**：提供生成、推理与行动选择的策略先验。
- **Harness／Host**：提供上下文、工具、执行介质、状态、权限、调度、观察与验证机制。
- **Purpose**：用户或组织托付的目标、角色、约束与验收标准，而不是 LLM 自发拥有的欲望。
- **Agent**：Purpose 在 Host 中实例化后的、带有持续状态和行动轨迹的运行过程。
- **Pragmatic Grounding**：符号生成通过行动接入环境反馈，并在独立验证下修正后续行动。
- **Harness Internalization**：经验证的交互轨迹经过归因与后训练，转化为模型的原生策略；记忆、Skill 和插件沉淀本身不等于权重内化。
- **DSH 的位置**：不只是另一个 Agent，而是可以编排多个子 Host 的 Meta-Host／Runtime Fabric。

## 五种 Harness 的工程重心

| Harness | 架构重心 |
| --- | --- |
| DSH | 让 Host 本身成为可组合、可替换的运行时对象 |
| Claude Code | 让行动循环可编程，并支持协同分工 |
| Codex | 让任务在明确边界内执行，并进行结果审查 |
| Pi dev | 缩减预设 Host，让用户和模型自行构造运行结构 |
| WorkBuddy | 让 Purpose 进入企业业务系统与组织工作流程 |

这些差异是架构重心，不是功能有无的绝对边界。

## 文档导航

- [架构论文：Agent 作为 Purpose 的 Runtime](docs/架构论文-Agent作为Purpose的Runtime.md)
- [本轮对话实录](docs/对话实录.md)
- [参考来源](docs/参考来源.md)
- [发布说明](PUBLISHING.md)

## 一句话总结

> LLM 提供行动策略的先验，Purpose 提供方向，Harness 提供现实作用面，State 提供持续主体性，而环境反馈赋予行动以真假。DSH 的价值，是让这些作用面本身也能被动态组装。

## 许可

当前未添加开源许可证。默认保留所有权利；如需允许他人复制、修改或再分发，请由仓库所有者明确选择许可证。
