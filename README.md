# AgentSpecGuard

**Stop AI agents from guessing, drifting, and overengineering.**

A lightweight AI Agent Skill that clarifies consequential requirements, locks task scope, and prevents unnecessary overengineering before execution.

[中文 README](./README.zh-CN.md)

> Clarify what matters. Lock the scope. Build only what was asked.

## The problem

New AI users often give agents a hard requirement in one sentence:

```text
Add authentication to my app.
```

The agent fills in the missing pieces by itself. That can turn into JWTs, refresh tokens, RBAC, Redis, migrations, extra abstractions, broad refactors, and a large test matrix — even when the user only wanted a simple password screen.

The opposite failure mode is just as bad: telling the model to “ask questions first” can make it interrogate the user about every harmless detail.

**AgentSpecGuard sits between those two extremes.**

It asks only when the missing answer would materially change the result. Once the consequential ambiguity is gone, it locks scope and pushes the agent toward the minimum complete solution.

## What it changes

### Without AgentSpecGuard

![Without AgentSpecGuard](./assets/Without%20AgentSpecGuard_EN.png)

### With AgentSpecGuard

![With AgentSpecGuard](./assets/With%20AgentSpecGuard_EN.png)

If the request is already clear, AgentSpecGuard asks **zero questions** and proceeds directly.

## Four gates, one small skill

### 1. Consequential clarification

Ask only when different answers would materially change behavior, architecture, scope, cost, risk, or the user's definition of success.

Safe, conventional, reversible defaults are inferred instead of pushed back to the user.

### 2. One question at a time

When clarification is needed, the agent asks the highest-impact unresolved question and briefly explains why it matters.

No giant questionnaire. No fake PRD ceremony.

### 3. Scope boundary

Before execution, the agent internally separates:

- **Required**
- **Allowed if necessary**
- **Out of scope**

Finding nearby ugly code is not permission to refactor it.

### 4. Minimum-complete engineering

AgentSpecGuard discourages common agentic overengineering patterns:

- silent error swallowing;
- unsupported fallback paths;
- speculative compatibility branches;
- abstractions for hypothetical future needs;
- unrelated refactors;
- disproportionate validation;
- hashes, synthetic IDs, benchmark harnesses, A/B tests, and similar machinery when the task does not require them.

It still validates real external boundaries and uses stronger checks for genuinely high-risk work.

## Install

AgentSpecGuard follows the open [Agent Skills](https://agentskills.io) `SKILL.md` format and has no runtime dependencies.

### Codex

Codex discovers user skills from `~/.agents/skills`:

```bash
git clone https://github.com/MoELuaNMaT/AgentSpecGuard.git ~/.agents/skills/agent-spec-guard
```

For a project-local install, clone or copy it into:

```text
<repo>/.agents/skills/agent-spec-guard/
```

### OpenCode

OpenCode also discovers `.agents/skills`, so the same global installation works:

```bash
git clone https://github.com/MoELuaNMaT/AgentSpecGuard.git ~/.agents/skills/agent-spec-guard
```

You can also use `.opencode/skills/agent-spec-guard/` or `.claude/skills/agent-spec-guard/`.

### Claude Code

Clone it into Claude Code's skills directory:

```bash
git clone https://github.com/MoELuaNMaT/AgentSpecGuard.git ~/.claude/skills/agent-spec-guard
```

### Other Agent Skills-compatible hosts

Place this repository wherever your host discovers a skill directory containing `SKILL.md`.

## Automatic activation

The skill description is intentionally front-loaded with common execution verbs such as **build, create, modify, fix, design, plan, configure, implement, migrate, and refactor** so compatible hosts can select it implicitly.

AgentSpecGuard should **not** activate for ordinary factual questions that can simply be answered.

Implicit skill selection is ultimately controlled by the host/model. A `SKILL.md` skill cannot cryptographically guarantee interception of every single prompt. If you need deterministic prompt injection on every request, a host-specific hook/plugin is the right enforcement layer; that is a natural future adapter for this project.

## Why not literally require “95% confidence”?

LLMs do not have a reliable calibrated internal probability that makes “I am 95% confident” meaningful.

AgentSpecGuard keeps the intent but replaces the number with observable exit criteria. The agent can start when:

- the desired outcome is clear;
- success can be judged;
- route-changing ambiguities are resolved;
- important non-defaultable constraints are known;
- remaining uncertainty can be handled with low-risk, reversible defaults.

The goal is not zero uncertainty. The goal is **zero consequential ambiguity**.

## Design philosophy

AgentSpecGuard is deliberately instruction-only.

No server. No API key. No telemetry. No package install. No model dependency.

The project is optimized for personal and small-team work, where agents frequently waste time by building infrastructure the user never asked for. If the repository clearly has stronger production, regulatory, safety, or security requirements, those concrete requirements take priority over the skill's lightweight defaults.

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
│   ├── without-agent-spec-guard-en.webp
│   ├── with-agent-spec-guard-en.webp
│   ├── without-agent-spec-guard-zh.webp
│   └── with-agent-spec-guard-zh.webp
├── agents/
│   └── openai.yaml
├── README.md
├── README.zh-CN.md
└── LICENSE
```

`agents/openai.yaml` keeps implicit invocation enabled and provides display metadata for OpenAI hosts that support it.

## Roadmap

Keep the core small. The useful next step is not to turn this into a framework; it is to add thin host-specific adapters where deterministic interception is valuable.

- [ ] Claude Code hook/plugin adapter
- [ ] OpenCode plugin adapter
- [ ] OMP extension adapter
- [ ] Small behavior eval set for ambiguous vs. non-ambiguous prompts

## License

MIT
