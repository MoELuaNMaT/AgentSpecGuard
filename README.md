# AgentSpecGuard

## Preflight for AI coding agents.

**Most coding agents start coding too early. AgentSpecGuard makes them clarify what matters, lock scope, and choose the minimum complete change before execution.**

`Claude Code` · `OpenAI Codex` · `OpenCode` · `Agent Skills` · `Zero dependencies`

> **AI should not ask 12 questions. It should not guess 12 answers either.**

AgentSpecGuard is a lightweight **AI Agent Skill** for requirement clarification, scope control, and overengineering prevention. It is designed for coding agents that can write code fast but may guess missing requirements, expand the task, refactor unrelated code, or build infrastructure nobody asked for.

**One `SKILL.md`. No server. No API key. No telemetry.**

[中文 README](./README.zh-CN.md)

## See the difference

### Without AgentSpecGuard

![Without AgentSpecGuard](./assets/Without%20AgentSpecGuard_EN.png)

### With AgentSpecGuard

![With AgentSpecGuard](./assets/With%20AgentSpecGuard_EN.png)

If the request is already clear, AgentSpecGuard asks **zero questions** and proceeds directly.

## The failure mode

A user asks:

```text
Add authentication to my app.
```

The agent has enough information to start coding, but not enough information to know **what the user actually means**. So it fills the gaps itself.

That can become JWTs, refresh-token rotation, RBAC, Redis, migrations, abstractions, broad refactors, and a large test matrix — even when the user only wanted a local password screen.

The opposite solution is not much better:

```text
Ask me about anything you are unsure about.
```

Now the agent can interrogate the user about colors, filenames, naming conventions, minor defaults, and other decisions that were never worth interrupting for.

AgentSpecGuard sits between those two extremes.

> **The goal is not zero uncertainty. The goal is zero consequential ambiguity.**

It asks only when the missing answer would materially change the result, architecture, scope, cost, risk, or definition of success. Everything else gets a conventional, low-risk, reversible default.

## One skill, four guardrails

### 1. Clarify consequential ambiguity

Ask only when different answers would materially change:

- user-visible behavior or the final result;
- architecture, data model, API, or implementation route;
- project scope or workload;
- compatibility that actually matters now;
- irreversible or destructive operations;
- meaningful security, privacy, cost, or operational risk;
- the criteria the user will use to call the task complete.

Safe, conventional, reversible defaults are inferred instead of pushed back to the user.

### 2. Ask one high-information question at a time

When clarification is necessary, the agent asks the **highest-impact unresolved question**, explains briefly why it matters, waits for the answer, and then reassesses.

No questionnaire. No fake PRD ceremony.

### 3. Lock the scope before execution

Before coding, the agent internally separates the task into:

- **Required**
- **Allowed if necessary**
- **Out of scope**

Finding nearby ugly code is not permission to refactor it.

### 4. Prefer the minimum complete change

AgentSpecGuard discourages common agentic overengineering patterns:

- silent error swallowing;
- unsupported fallback paths;
- speculative compatibility branches;
- abstractions for hypothetical future needs;
- unrelated refactors;
- verification effort disproportionate to actual risk;
- hashes, synthetic IDs, benchmark harnesses, A/B tests, and similar machinery when the task does not require them.

This is **not** “never engineer defensively.” External inputs and real system boundaries should still be validated, and genuinely high-risk work should receive stronger verification.

## Why this exists

Overengineering is a recurring complaint from coding-agent users. Recent discussions describe small tasks turning into large diffs, unrequested refactors, extra abstractions, and solutions that the same model can later simplify without losing functionality.

- [Codex users: “I am tired of Codex over engineering everything”](https://www.reddit.com/r/codex/comments/1vk7p9q/i_am_tired_of_codex_over_engineering_everything/)
- [Codex / Claude users: “How do you stop ... overengineering small coding tasks?”](https://www.reddit.com/r/codex/comments/1vf5elq/how_do_you_stop_codexclaude_from_overengineering/)
- [Claude Code users: “How to avoid overengineering?”](https://www.reddit.com/r/ClaudeCode/comments/1uepqau/how_to_avoid_overengineering/)

A common answer is “write a more specific prompt.” AgentSpecGuard turns that advice into a reusable pre-execution workflow instead of requiring every user to become a prompt engineer.

## Quick install

AgentSpecGuard follows the open [Agent Skills](https://agentskills.io) `SKILL.md` format.

### Codex

```bash
git clone https://github.com/MoELuaNMaT/AgentSpecGuard.git ~/.agents/skills/agent-spec-guard
```

Project-local:

```text
<repo>/.agents/skills/agent-spec-guard/
```

### OpenCode

```bash
git clone https://github.com/MoELuaNMaT/AgentSpecGuard.git ~/.agents/skills/agent-spec-guard
```

OpenCode can also use `.opencode/skills/agent-spec-guard/` or `.claude/skills/agent-spec-guard/`.

### Claude Code

```bash
git clone https://github.com/MoELuaNMaT/AgentSpecGuard.git ~/.claude/skills/agent-spec-guard
```

### Other Agent Skills-compatible hosts

Place the repository wherever your host discovers a skill directory containing `SKILL.md`.

## Automatic activation

The skill description intentionally includes common execution verbs such as **build, create, modify, fix, design, plan, configure, implement, migrate, and refactor** so compatible hosts can select it implicitly when a user asks an agent to make something.

Ordinary factual questions should not activate AgentSpecGuard.

Implicit skill selection is ultimately controlled by the host and model. A `SKILL.md` skill cannot guarantee interception of every prompt. Deterministic per-message interception requires a host-specific Hook / Plugin / Extension adapter; those adapters are the natural next step for this project.

## Why not literally require “95% confidence”?

LLMs do not expose a reliably calibrated internal probability where “I am 95% confident” means what it sounds like.

AgentSpecGuard keeps the intent and replaces the fake precision with observable exit criteria. Execution can begin when:

- the desired outcome is clear;
- success can be judged;
- route-changing ambiguities are resolved;
- important non-defaultable constraints are known;
- remaining uncertainty can be handled with low-risk, reversible defaults.

## Compatibility

| Host | Skill format | Implicit matching | Notes |
| --- | --- | --- | --- |
| Codex | Agent Skills | Yes | `.agents/skills` supported |
| OpenCode | Agent Skills | Yes | `.agents`, `.opencode`, and Claude-compatible paths supported |
| Claude Code | Agent Skills | Yes | Install under `.claude/skills` |
| Other compatible agents | Agent Skills | Host-dependent | Requires `SKILL.md` discovery |

## Repository layout

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

`agents/openai.yaml` keeps implicit invocation enabled and provides display metadata for OpenAI hosts that support it.

## Roadmap

Keep the core small. The useful next step is not to turn this into another framework; it is to add thin host-specific adapters where deterministic interception is valuable.

- [ ] Claude Code Hook / Plugin adapter
- [ ] OpenCode Plugin adapter
- [ ] OMP Extension adapter
- [ ] Small behavior eval set for ambiguous vs. non-ambiguous prompts

## License

MIT
