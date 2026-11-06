# Agent 作为 Purpose 的 Runtime：从 Pragmatic Grounding 到 Host of Hosts

## 摘要

Pragmatic Grounding（语用接地）不是“给 LLM 多接几个工具”，而是让符号生成进入“目的—行动—环境反馈—验证—状态更新”的闭环。本文以

```text
Agentₚ = RunH(LLM, Purpose, State)
```

为核心，区分 LLM、Harness／Host、Purpose 与 Agent，并比较 DSH、Claude Code、Codex、Pi dev 和 WorkBuddy 的工程路线。进一步地，DSH 可以被理解为一个 Meta-Host：其他 Harness 不必被压平成普通 Agent，而可以作为嵌套子 Host，由 DSH 通过适配器进行委派、观察、调度和结果回收。

## 1. Pragmatic Grounding 的两个闭环

### 1.1 运行时接地闭环

```text
Purpose
  → LLM 生成符号与行动
  → Harness 将行动作用于环境
  → 环境状态发生变化
  → 观察、报错、测试结果返回模型
  → 模型修正下一步行动
```

模型生成的符号不再只是“关于行动的描述”，而成为真实状态转移的一部分。代码、编译器、测试框架和文件系统之所以能形成强语用接地，是因为它们能提供边界较清晰、返回较及时的环境反馈。

### 1.2 学习／内化闭环

```text
经验证的交互轨迹
  → 清洗、结果验证、因果归因
  → 后训练或轨迹微调
  → 新模型形成更强的行动先验
  → 减少对外部流程提示的依赖
```

这里必须区分三种经验沉淀：

1. **写进上下文、记忆或知识库**：下次可以读取；
2. **沉淀为 Skill、Hook、插件或工作流**：下次可以调用；
3. **进入模型权重**：成为不依赖显式外部提示的原生策略。

前两种是外化经验的保存与复用，只有第三种才是严格意义上的 **Harness Internalization**。不能由“支持记忆、Skill 或插件”直接推导出“已经发生后训练内化”。

最后还需保留一条边界：**策略内化不等于后果经验内化**。模型可以学会如何把事情办成，却并不因此亲自承担资金损失、数据破坏或社会关系受损。

## 2. Agentₚ = RunH(LLM, Purpose, State)

### 2.1 四层对象

- **LLM**：提供生成、推理和行动选择的策略先验。
- **Harness／Host H**：提供上下文、工具、执行介质、状态、权限、调度、观察和验证机制。
- **Purpose p**：用户或组织托付的目标、角色、约束与验收标准。
- **Agent**：Purpose 在 Host 中被实例化后的、带有持续状态和行动轨迹的运行过程。

这一表达将 `Agent = Model + Harness` 中的静态加号换成了动态的 `Run`。Agent 不再是组件清单，而是一个运行过程。

由此可以得到几个推论：

- Agent 不是一段 persona Prompt；
- Subagent 不是另一个模型，而是另一个具有独立目的和上下文的运行实例；
- 同一个 Agent 可以更换底层模型，而保持目的与任务身份不变；
- 同一个模型可以在 Host 中同时支撑多个不同 Purpose 的 Agent；
- Purpose 首先来自用户、组织或现实任务，LLM 生成的是对 Purpose 的理解、子目标与行动计划。

### 2.2 把环境与轨迹显式补出

若要突出 Pragmatic Grounding，可以进一步写成：

```text
Agentₚ = RunH,E(LLM, Purpose, State)
              ↓
Trajectory＝[(State₀, Action₀, Observation₀),
             (State₁, Action₁, Observation₁),
             …,
             Outcome]
```

其中：

- **H** 决定行动如何执行；
- **E** 是行动作用的真实环境；
- **State** 保持时间与身份连续；
- **Observation** 让环境能够反驳或确认模型；
- **Outcome** 表示 Purpose 是否真正达成；
- **Trajectory** 提供可验证、可回放、可归因的经验材料。

Agent 的最终产物因此不只是 Answer，而是 **Outcome + Trajectory**。

## 3. 五种 Harness 的工程路线

下表描述架构重心，不应被理解为功能互斥。

| Harness | Host 的主要形态 | Purpose 的主要实例 | 主要真值来源 | 经验首先沉淀到 |
| --- | --- | --- | --- | --- |
| DSH | 可分层组合的插件树 | 配置化运行环境中的任务过程 | 工作区、工具、命令、沙箱及其返回 | 插件、Profile、配置与运行轨迹 |
| Claude Code | 有明确产品意见的编码引擎和生命周期 | 主会话、子代理及协同任务 | 代码库、测试、diff、Hook、人的即时纠偏 | CLAUDE.md、记忆、Skills、Hooks |
| Codex | 本地／云端受约束的任务执行环境 | 被委派的编码任务 | 沙箱内执行结果、测试、变更与审查 | AGENTS.md、Skills、插件和环境配置 |
| Pi dev | 极简 Agent Loop、TUI 与 SDK | 用户自行组装的工作流 | 文件、Shell 及所安装扩展的反馈 | Extension、Skill、Prompt、Package |
| WorkBuddy | 企业工作台与托管 Agent 运行时 | Expert、Assistant、Team、企业自定义 Agent | 业务文件、企业系统回执、交付物与组织评价 | Skills、知识库、专家模板、轨迹与评估 |

### 3.1 DSH：让 Host 本身成为可计算对象

DSH 的关键不是插件数量，而是模型适配器、工具注册表、会话日志乃至 Agent Loop 本身都可以成为可替换插件；运行实例由不同层次组合成插件树。

这使“什么样的环境最适合这个 Purpose”成为工程设计空间，而不是由产品一次性决定。DSH 的独特贡献是 Host 的可塑性与可替换性。

但可组合性不等于接地真实性。换掉 Agent Loop 不保证行动更正确，增加插件也不保证反馈更真实。DSH 仍需要独立验收器、权限边界、轨迹归因和结果回放。

### 3.2 Claude Code：可编程的协同行动循环

Claude Code 以代码库读取、文件编辑和命令执行为基本闭环，并通过 CLAUDE.md、自动记忆、Skills 与 Hooks 接入项目规范、跨会话经验和重复工作流程。

其代表性在于生命周期可编程：Hook 可以插入会话、回合和工具调用等节点；子代理则使 Purpose 能被拆成多个边界明确的运行实例。人的位置不只是最后审查者，也可以持续存在于行动循环中。

主要风险是生成者、总结者和验证者如果始终是同一个模型，闭环可能退化为“自己证明自己正确”。独立测试、确定性 Hook 和人的验收仍然重要。

### 3.3 Codex：有硬边界的任务执行与结果审查

Codex 的架构重心是先规定 Agent 可以自主行动的技术边界，再让 Purpose 在边界内持续运行。沙箱约束实际能力，审批决定何时必须询问；云端任务工作区则提供相互隔离的执行环境。

与 Claude Code 的重心相比：Claude Code 更强调行动循环的可编程协同，Codex 更强调执行边界、任务隔离与结果审查。这是治理思路的差异，不是绝对的功能差异。

沙箱能证明行动发生在哪里，测试能证明某些程序性质成立，但二者都不能证明最初的目的值得做，也不能证明代码变更一定产生产品价值。

### 3.4 Pi dev：通过减法让使用者构造 Host

Pi 选择极简、可扩展的路线，减少预设工作流，让使用者通过扩展、Skills、Prompt 模板和 Package 构造自己的 Host。

DSH 与 Pi 的区别可以概括为：

- **DSH**：把 Host 的每个组成都变成可替换对象；
- **Pi**：先尽量移除预设，只保留最小核心，让使用者决定 Host 应该长什么样。

Pi 使“构造 Harness”本身成为任务的一部分。成功经验可以转化为代码、扩展或技能，而不是只留在一次会话里。但极简并不自动等于更好的接地，验证器、权限边界和经验固化标准仍需使用者自行建设。

### 3.5 WorkBuddy：从代码真值转向组织工作真值

WorkBuddy 将 Experts、Skills、知识库、多平台连接、定时任务和业务交付物放入企业工作环境。用本文框架映射：Expert 更接近可复用的 Purpose 模板，Assistant／Team 是持续运行与协同执行的 Agent 形态，Managed Agents 则提供托管 Host 基础设施。

接地点随之变化：

```text
Coding Harness：
代码修改 → 编译／测试 → diff／PR

Work Harness：
业务目标 → 文件与企业系统操作 → 交付物／系统回执
          → 人、团队和业务结果的验收
```

“生成了一份可打开的报告”不等于“报告结论正确”；“成功发送了一封邮件”不等于“实现了沟通目的”。因此，这条路线尤其需要业务语义级 Eval、独立验收、权限分级、审计与责任归属。

## 4. DSH 作为 Meta-Host：嵌套而不是压平

其他 Harness 可以被外部系统调用，但在概念上不应把整个 Harness 压平成一个 Agent 插件。更准确的结构是：

```text
DSH：Meta-Host／Host of Hosts
  │
  ├── 插件适配器：启动、观察、打断、恢复、收集结果
  │
  └── 子 Harness：Claude Code／Codex／Pi／WorkBuddy
          │
          └── Agentₚᵢ：具体 Purpose 的运行实例
```

公式可以展开为：

```text
Agentₚ = RunDSH(LLM₀, Purpose, State₀)
              │
              └── Delegate(Purposeᵢ)
                    → RunHᵢ(LLMᵢ, Purposeᵢ, Stateᵢ)
```

这里需要区分：

1. **DSH 插件**：调用子 Harness 的适配器；
2. **子 Harness**：继续提供自己的执行环境、权限、状态和反馈机制；
3. **子 Agent**：具体 Purpose 在该环境中的 Runtime。

DSH 获得的是跨 Host 的编排权，而不是绕过每个子 Host 的内部安全边界。其理想返回值也不只是“子 Agent 说了什么”，而是“子 Agent 做了什么、环境返回了什么、凭什么认为完成了”。

## 5. 行动力与 Purpose 的关系

更准确的命题不是“行动力比 Purpose 更重要”，而是：

> AI 的可验证行动力，比 LLM 对 Purpose 的推演、分解和语言表述更重要。

没有行动力的 Purpose 只是愿望；没有 Purpose 约束的行动力只是失控的功率。真正重要的能力是：

```text
在 Purpose 约束下改变环境
→ 读取真实反馈
→ 修正后续行动
→ 留下可验证的轨迹
→ 对行动后果保持可治理性
```

## 6. 结论

五种 Harness 不是同一条排行榜，而是在不同层面突破：

- **DSH 让 Host 可计算**；
- **Pi 让 Host 可自建**；
- **Claude Code 让行动循环可编程**；
- **Codex 让任务执行可界定**；
- **WorkBuddy 让 Purpose 进入组织**。

最终，LLM 提供行动策略的先验，Purpose 提供方向，Harness 提供现实作用面，State 提供持续主体性，而环境反馈赋予行动以真假。DSH 的价值，是让这些作用面本身也能被动态组装。

越接近真实业务世界，Pragmatic Grounding 就越不只是能力问题，而同时成为 Exposure Governance（敞口治理）问题：谁授权、谁验收、谁能够回滚，以及不可逆后果由谁承担。
