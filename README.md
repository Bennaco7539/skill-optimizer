# Skill Optimizer

[中文版](./README.zh-CN.md)

Diagnose and optimize your [Agent Skills](https://agentskills.io) (SKILL.md files) using **real session data + research-backed static analysis**. Get a prioritized report with P0/P1/P2 fixes.

Works with **Claude Code**, **Codex**, and any agent supporting the Agent Skills open standard. Auto-detects your platform and scans the right paths.

Most skill auditors only do static checks on your SKILL.md. This one also mines your actual session transcripts to measure trigger rates, user satisfaction, workflow completion, and undertrigger gaps — then scores each skill on a 5-point composite scale.

## What It Does

**6 scored dimensions** (weighted into composite score):

| Dimension | What's Measured |
|-----------|----------------|
| **Trigger Rate** | How often is the skill actually invoked vs. how often it should be? |
| **User Reaction** | Does the user accept, correct, or reject the skill after invocation? |
| **Workflow Completion** | How far through the skill's defined steps does execution get? |
| **Static Quality** | 14 checks: YAML safety, CSO compliance, info position, word count, etc. |
| **Undertrigger** | Missed opportunities — user needed the skill but it wasn't invoked |
| **Token Economics** | Cost-effectiveness and progressive disclosure tier compliance |

**3 qualitative dimensions** (reported but not scored):

| Dimension | What's Measured |
|-----------|----------------|
| **Overtrigger** | False positives — skill fired but user didn't want it |
| **Cross-Skill Conflicts** | Trigger keyword overlap and contradictory guidance between skills |
| **Environment Consistency** | Broken file paths, missing CLI tools, non-existent directories |

## Installation

Copy the command below and paste it directly into your agent's chat — it will install automatically:

### Claude Code

```
Install the skill-optimizer skill from https://github.com/hqhq1025/skill-optimizer
```

### Codex

```
Install the skill-optimizer skill from https://github.com/hqhq1025/skill-optimizer into ~/.codex/skills/
```

### Other Agents (Cursor, OpenCode, Gemini CLI, etc.)

```
Install the skill-optimizer skill from https://github.com/hqhq1025/skill-optimizer into ~/.agents/skills/
```

<details>
<summary>Manual install</summary>

```bash
# Claude Code
git clone https://github.com/hqhq1025/skill-optimizer.git /tmp/skill-optimizer
cp -r /tmp/skill-optimizer/skills/skill-optimizer ~/.claude/skills/
rm -rf /tmp/skill-optimizer

# Codex
git clone https://github.com/hqhq1025/skill-optimizer.git /tmp/skill-optimizer
cp -r /tmp/skill-optimizer/skills/skill-optimizer ~/.codex/skills/
rm -rf /tmp/skill-optimizer

# Shared (any agent)
git clone https://github.com/hqhq1025/skill-optimizer.git /tmp/skill-optimizer
cp -r /tmp/skill-optimizer/skills/skill-optimizer ~/.agents/skills/
rm -rf /tmp/skill-optimizer
```

</details>

## Usage

```
/optimize-skill              # Scan all skills
/optimize-skill my-skill     # Single skill
/optimize-skill skill-a skill-b  # Multiple skills
```

The skill generates a diagnostic report with:
- **Overview table** — all skills at a glance with scores
- **P0 Fixes** — blocking issues that must be resolved
- **P1 Improvements** — experience improvements
- **P2 Optimizations** — optional tweaks
- **Per-skill diagnostics** — all 8 dimensions for each skill

## Multi-Platform Session Analysis

The optimizer auto-detects available platforms and scans session data from all of them:

| Platform | Skills Path | Session Data Path |
|----------|------------|-------------------|
| Claude Code | `~/.claude/skills/` | `~/.claude/projects/**/*.jsonl` |
| Codex | `~/.codex/skills/` | `~/.codex/sessions/**/*.jsonl` |
| Shared | `~/.agents/skills/` | — |

## Research Background

The analysis dimensions are grounded in peer-reviewed research:

| Research | Key Finding | Applied In |
|----------|-------------|------------|
| [Memento-Skills](https://arxiv.org/abs/2603.18743) (2026) | Skills as structured files require accurate routing; unrouted skills cannot self-improve via the read-write learning loop | Undertrigger detection, compounding risk assessment |
| [MCP Description Quality](https://arxiv.org/abs/2602.18914) (2026) | Well-written descriptions achieve 72% tool selection rate vs. 20% random baseline (3.6x improvement) | Description quality checks, evidence-based rewrite suggestions |
| [Lost in the Middle](https://arxiv.org/abs/2307.03172) (Liu et al., TACL 2024) | LLM attention follows a U-shaped curve — middle content is ignored | Critical info position check |
| [Prompt Format Impact](https://arxiv.org/abs/2411.10541) (He et al., 2024) | Format changes alone cause 9-40% performance variance | Static quality analysis |
| [IFEval](https://arxiv.org/abs/2311.07911) (Zhou et al., 2023) | LLMs struggle with multi-constraint prompts | Trigger condition count check |
| [Meincke et al.](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=5357179) (2025) | Persuasion directives have inconsistent effects across models | MUST/NEVER density guidance |

## How It Works

```
Identify target skills (scan ~/.claude/skills/, ~/.codex/skills/, ~/.agents/skills/)
        ↓
Collect session data (auto-detect platform, scan JSONL transcripts)
        ↓
Run 8 analysis dimensions (6 scored + 3 qualitative)
        ↓
Compute composite scores (weighted average of 6 scored dimensions)
        ↓
Output report with P0/P1/P2 prioritized fixes
```

**Scored dimensions (weighted average):**
- Trigger rate: 25%
- User reaction: 20%
- Workflow completion: 15%
- Static quality: 15%
- Undertrigger: 15%
- Token economics: 10%

**Qualitative dimensions** (overtrigger, cross-skill conflicts, environment consistency) are reported with examples but do not affect the numeric score.

## Compatibility

Works with any agent that supports the [Agent Skills](https://agentskills.io) open standard:
- Claude Code
- Codex
- Cursor
- OpenCode
- Gemini CLI

## Community

- [LINUX DO](https://linux.do) — Where we first shared this project

## License

MIT
