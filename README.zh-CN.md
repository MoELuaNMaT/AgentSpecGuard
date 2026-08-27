# AgentSpecGuard

**阻止 AI Agent 猜需求、跑偏和过度工程化。**

一个轻量级 AI Agent Skill：执行前自动澄清真正会影响结果的需求，锁定任务范围，并抑制需求越界、顺手重构和不必要的过度工程化。

[English README](./README.md)

> 澄清真正重要的事，锁定范围，只做用户真正要求的事情。

## 它解决什么问题？

很多刚开始使用 AI 的用户会直接丢给 Agent 一句硬需求：

```text
给我的应用加一个登录功能。
```

然后 AI 会自行补全大量没有被说明的需求：JWT、Refresh Token、RBAC、Redis、数据库迁移、额外抽象层、大规模重构、整套测试……

但用户真正想要的可能只是一个简单的本地密码登录页面。

另一个极端也不好：如果只告诉 AI“有不清楚的地方就问我”，它很容易开始对无关痛痒的细节连续提问，把用户变成填 PRD 的人。

**AgentSpecGuard 的目标就是卡在这两个极端之间。**

它只追问会真正改变结果的歧义；一旦关键需求明确，就锁定范围，并要求 AI 采用满足当前需求的最小完整实现。

## 使用前后

没有 AgentSpecGuard：

```text
你
“加一个用户登录。”

AI
✓ 加入 JWT
✓ 加入 Refresh Token Rotation
✓ 加入 Redis Session
✓ 加入 RBAC
✓ 重构 Auth 模块
✓ 新增 18 个测试

你
“……我只是想要一个本地密码页面。”
```

使用 AgentSpecGuard：

```text
你
“加一个用户登录。”

AI
问题：你希望使用本地用户名/密码登录，还是 Google 等第三方登录？

为什么要问：这个选择会直接改变认证架构和实现范围。

你
“本地用户名密码。”

AI
明白。我会实现最小完整的本地登录流程，不主动加入 OAuth、
RBAC、Redis 或无关的认证基础设施。
```

如果你的需求本身已经足够明确，AgentSpecGuard 会 **一个问题都不问，直接执行**。

## 四个 Gate

### 1. 只澄清“有后果的歧义”

只有当不同答案会明显改变以下内容时才提问：

- 用户最终看到的行为或结果；
- 架构、数据模型、接口或主要技术路线；
- 项目范围和工作量；
- 当前真正需要的兼容性；
- 不可逆或破坏性操作；
- 明显的成本、安全、隐私或运行风险；
- 用户判断“完成”的关键标准。

如果一个未说明的细节存在常规、低风险、可逆的默认值，就由 AI 自己选择，不要把问题甩回给用户。

### 2. 每次只问一个问题

确实需要确认时：

1. 只问一个；
2. 优先问最影响最终结果的问题；
3. 简短说明“为什么要问”；
4. 得到答案后再判断是否还有必要继续问。

不会一次丢出十几个问题，也不会强行让用户填写 PRD。

### 3. 锁定 Scope

执行前，AI 在内部把工作划分成：

- **必须做**
- **必要时允许做**
- **超出范围**

看到旁边有一段代码很丑，不代表获得了顺手重构它的授权。

### 4. 最小完整工程化

AgentSpecGuard 会压制 AI Agent 常见的过度工程化行为：

- 静默吞错；
- 没有依据的 fallback；
- 为假想兼容性增加分支；
- 为“以后可能用到”增加抽象层；
- 顺手重构无关代码；
- 与风险完全不匹配的验证强度；
- 在没有需求时主动增加 hash、test ID、benchmark、A/B test 等额外验证机制。

但它不是“禁止工程化”。真实的外部输入和系统边界仍然应该验证；支付、迁移、安全、破坏性修改等高风险工作也应该使用更严格的验证。

## 安装

AgentSpecGuard 遵循开放的 [Agent Skills](https://agentskills.io) `SKILL.md` 格式，不需要运行时、不需要 API Key、没有额外依赖。

### Codex

Codex 可以从 `~/.agents/skills` 发现用户级 Skill：

```bash
git clone https://github.com/MoELuaNMaT/AgentSpecGuard.git ~/.agents/skills/agent-spec-guard
```

也可以只给某个项目安装：

```text
<项目目录>/.agents/skills/agent-spec-guard/
```

### OpenCode

OpenCode 同样支持 `.agents/skills`，所以可以直接使用同一套安装方式：

```bash
git clone https://github.com/MoELuaNMaT/AgentSpecGuard.git ~/.agents/skills/agent-spec-guard
```

同时也支持 `.opencode/skills/agent-spec-guard/` 和 `.claude/skills/agent-spec-guard/`。

### Claude Code

```bash
git clone https://github.com/MoELuaNMaT/AgentSpecGuard.git ~/.claude/skills/agent-spec-guard
```

### 其他支持 Agent Skills 的工具

把本仓库放到该工具能够发现 `SKILL.md` 的 Skill 目录即可。

## 自动调用

为了提高自动触发概率，`SKILL.md` 的 description 把 **build / create / modify / fix / design / plan / configure / implement / migrate / refactor** 等执行型关键词放在最前面。

因此在支持隐式 Skill Matching 的 Agent 中，只要用户提出“做一个东西 / 改一个东西 / 修一个东西”之类的请求，它就有机会自动加载。

普通事实问答不应该触发 AgentSpecGuard。

需要说明的是：**Skill 的自动选择最终由宿主和模型决定。** 单纯一个 `SKILL.md` 无法百分之百保证拦截每一次输入。如果以后需要“每条用户输入后都强制拼接提示词”这种确定性行为，更正确的实现方式是给不同 Harness 做 Hook / Plugin / Extension 适配器，而不是假装 Skill 自身能保证这一点。

## 为什么没有真的要求“达到 95% 自信”？

LLM 并不存在可靠校准的“我现在有 95% 把握”数值。

所以 AgentSpecGuard 保留这个目标，但把它改成可以观察的退出条件：

- 最终目标已经明确；
- 有办法判断任务是否完成；
- 会改变技术路线或最终结果的歧义已经消除；
- 无法安全自行决定的重要约束已经知道；
- 剩余的不确定项都有低风险、可逆的常规默认值。

目标不是消灭所有不确定性，而是消灭 **会造成实质后果的不确定性**。

## 设计原则

AgentSpecGuard 故意保持为 instruction-only Skill：

- 无服务器
- 无 API Key
- 无遥测
- 无包依赖
- 不绑定模型

它主要面向个人项目和小团队项目，重点解决“AI 为用户没有提出的需求主动建设一大堆基础设施”的问题。

如果项目本身明确属于生产、高安全、监管或其他高风险环境，则项目里的真实约束优先于本 Skill 的轻量默认策略。

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
├── agents/
│   └── openai.yaml
├── README.md
├── README.zh-CN.md
└── LICENSE
```

`agents/openai.yaml` 为支持该元数据的 OpenAI 宿主提供展示信息，并明确允许隐式调用。

## 后续方向

核心 Skill 应该继续保持小，而不是慢慢膨胀成一个新 Framework。

真正值得增加的是薄适配层，让“自动拦截”从语义匹配升级成确定性行为：

- [ ] Claude Code Hook / Plugin
- [ ] OpenCode Plugin
- [ ] OMP Extension
- [ ] 一组很小的行为 Eval：测试哪些需求该问、哪些需求不该问

## License

MIT
