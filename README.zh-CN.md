# AgentSpecGuard

## AI Coding Agent 的执行前 Preflight

**大多数 Coding Agent 的问题，不是不会写代码，而是太早开始写。AgentSpecGuard 会在执行前澄清真正重要的需求、锁定范围，并要求 AI 采用最小完整改动。**

`Claude Code` · `OpenAI Codex` · `OpenCode` · `Agent Skills` · `零依赖`

> **AI 不应该追问你 12 个问题，也不应该擅自替你猜 12 个答案。**

AgentSpecGuard 是一个轻量级 **AI Agent Skill**，专门处理需求澄清、Scope Control 和过度工程化。它面向那些写代码很快，但容易自行补全需求、扩大任务范围、顺手重构、增加额外基础设施的 Coding Agent。

**一个 `SKILL.md`。无服务器。无 API Key。无遥测。**

[English README](./README.md)

## 先看使用前后

### 未使用 AgentSpecGuard

![未使用 AgentSpecGuard](./assets/Without%20AgentSpecGuard_CN.png)

### 使用 AgentSpecGuard

![使用 AgentSpecGuard](./assets/With%20AgentSpecGuard_CN.png)

如果需求本身已经足够明确，AgentSpecGuard 会 **一个问题都不问，直接执行**。

## 真正的问题是什么？

用户只说：

```text
给我的应用加一个登录功能。
```

AI 已经有足够信息开始写代码，但还没有足够信息知道 **用户真正想要的是什么**。

于是它会自己补全缺失需求：JWT、Refresh Token、RBAC、Redis、数据库迁移、额外抽象层、大规模重构、整套测试……

但用户真正想要的，可能只是一个本地密码登录页面。

另一个极端也同样糟糕：

```text
只要有任何不确定的地方都先问我。
```

这时 AI 又可能开始追问颜色、文件名、命名方式、无关默认值，把用户变成填写 PRD 的人。

AgentSpecGuard 就卡在这两个极端之间。

> **目标不是消灭所有不确定性，而是消灭会造成实际后果的歧义。**

它只追问那些会真正改变最终结果、架构、范围、成本、风险或完成标准的问题；其余细节直接使用常规、低风险、可逆的默认值。

## 一个 Skill，四道 Guardrail

### 1. 只澄清“有后果的歧义”

只有当不同答案会明显改变以下内容时才提问：

- 用户最终看到的行为或结果；
- 架构、数据模型、接口或主要技术路线；
- 项目范围和工作量；
- 当前真正需要的兼容性；
- 不可逆或破坏性操作；
- 明显的安全、隐私、成本或运行风险；
- 用户判断“完成”的关键标准。

存在常规、低风险、可逆默认值的细节，由 AI 自己处理，不把问题甩回给用户。

### 2. 每次只问一个信息量最高的问题

确实需要确认时，AI 只问当前**最影响最终结果的那个问题**，简短说明为什么要问，得到答案后再重新判断是否还需要继续确认。

不一次抛十几个问题，也不强行让用户填写 PRD。

### 3. 执行前锁定 Scope

AI 在内部把任务分成：

- **必须做**
- **必要时允许做**
- **超出范围**

看到旁边有一段代码很丑，不代表获得了顺手重构它的授权。

### 4. 默认选择最小完整改动

AgentSpecGuard 会压制 Coding Agent 常见的过度工程化行为：

- 静默吞错；
- 没有依据的 fallback；
- 为假想兼容性增加分支；
- 为“以后可能用到”增加抽象层；
- 顺手重构无关代码；
- 与实际风险完全不匹配的验证强度；
- 在没有需求时主动增加 hash、test ID、benchmark、A/B test 等额外机制。

这不是“禁止防御性编程”。真实外部输入和系统边界仍然应该验证；支付、迁移、安全、破坏性修改等高风险工作也应该使用更严格的验证。

## 为什么做这个 Skill？

“AI 把一个小任务越做越大”并不是一个假想问题。最近 Coding Agent 社区反复出现同一类反馈：小改动变成巨大 diff、无关重构、额外抽象，以及模型明明可以在之后把自己的实现简化，却一开始没有选择更简单的方案。

- [Codex 用户：I am tired of Codex over engineering everything](https://www.reddit.com/r/codex/comments/1vk7p9q/i_am_tired_of_codex_over_engineering_everything/)
- [Codex / Claude 用户：How do you stop ... overengineering small coding tasks?](https://www.reddit.com/r/codex/comments/1vf5elq/how_do_you_stop_codexclaude_from_overengineering/)
- [Claude Code 用户：How to avoid overengineering?](https://www.reddit.com/r/ClaudeCode/comments/1uepqau/how_to_avoid_overengineering/)

社区最常见的建议之一是：“把 Prompt 写得更具体。”

AgentSpecGuard 想做的事情，是把这件事变成一个可重复使用的执行前流程，而不是要求每个新用户先学会 Prompt Engineering。

## 快速安装

AgentSpecGuard 遵循开放的 [Agent Skills](https://agentskills.io) `SKILL.md` 格式。

### Codex

```bash
git clone https://github.com/MoELuaNMaT/AgentSpecGuard.git ~/.agents/skills/agent-spec-guard
```

项目级安装：

```text
<项目目录>/.agents/skills/agent-spec-guard/
```

### OpenCode

```bash
git clone https://github.com/MoELuaNMaT/AgentSpecGuard.git ~/.agents/skills/agent-spec-guard
```

OpenCode 也可以使用 `.opencode/skills/agent-spec-guard/` 或 `.claude/skills/agent-spec-guard/`。

### Claude Code

```bash
git clone https://github.com/MoELuaNMaT/AgentSpecGuard.git ~/.claude/skills/agent-spec-guard
```

### 其他支持 Agent Skills 的工具

把本仓库放到该工具能够发现 `SKILL.md` 的 Skill 目录即可。

## 自动调用

为了提高自动触发概率，Skill 的 description 中包含了 **build / create / modify / fix / design / plan / configure / implement / migrate / refactor** 等执行型关键词。

因此在支持隐式 Skill Matching 的 Agent 中，当用户提出“做一个东西 / 改一个东西 / 修一个东西”时，它有机会自动加载。

普通事实问答不应该触发 AgentSpecGuard。

需要说明的是：**Skill 的自动选择最终由宿主和模型决定。** 一个 `SKILL.md` 无法百分之百保证拦截每次输入。真正确定性的逐消息拦截，需要针对不同 Harness 实现 Hook / Plugin / Extension；这也是项目后续最自然的扩展方向。

## 为什么没有真的要求“达到 95% 自信”？

LLM 并不存在可靠校准的“我现在有 95% 把握”数值。

所以 AgentSpecGuard 保留这个目标，但改成可观察的退出条件：

- 最终目标已经明确；
- 有办法判断任务是否完成；
- 会改变技术路线或最终结果的歧义已经消除；
- 无法安全自行决定的重要约束已经知道；
- 剩余不确定项都有低风险、可逆的常规默认值。

## 兼容性

| 工具 | Skill 格式 | 自动匹配 | 说明 |
| --- | --- | --- | --- |
| Codex | Agent Skills | 支持 | 支持 `.agents/skills` |
| OpenCode | Agent Skills | 支持 | 支持 `.agents` / `.opencode` / Claude 兼容路径 |
| Claude Code | Agent Skills | 支持 | 可安装到 `.claude/skills` |
| 其他兼容 Agent | Agent Skills | 取决于宿主 | 需要支持 `SKILL.md` 发现机制 |

## 仓库结构

```text
AgentSpecGuard/
├── SKILL.md
├── assets/
│   ├── Without AgentSpecGuard_EN.png
│   ├── With AgentSpecGuard_EN.png
│   ├── Without AgentSpecGuard_CN.png
│   └── With AgentSpecGuard_CN.png
├── agents/
│   └── openai.yaml
├── README.md
├── README.zh-CN.md
└── LICENSE
```

`agents/openai.yaml` 为支持该元数据的 OpenAI 宿主提供展示信息，并明确允许隐式调用。

## 后续方向

核心 Skill 应该继续保持小，而不是慢慢膨胀成一个新的 Framework。

真正值得增加的是薄适配层，让“自动拦截”从语义匹配升级成确定性行为：

- [ ] Claude Code Hook / Plugin
- [ ] OpenCode Plugin
- [ ] OMP Extension
- [ ] 一组很小的行为 Eval：测试哪些需求该问、哪些需求不该问

## License

MIT
