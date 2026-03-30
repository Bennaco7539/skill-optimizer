# Skill Optimizer

[English](./README.md)

诊断和优化你的 [Agent Skills](https://agentskills.io)（SKILL.md 文件）— 基于**真实 session 数据 + 学术研究支撑的静态分析**，输出 P0/P1/P2 优先级修复报告。

支持 **Claude Code**、**Codex** 以及所有兼容 Agent Skills 开放标准的 agent。自动检测平台并扫描对应路径。

大多数 skill 审计工具只做 SKILL.md 的静态检查。这个工具还会挖掘你的真实 session 记录，量化触发率、用户满意度、workflow 完成率和漏触发缺口，最终为每个 skill 打出 5 分制综合评分。

## 功能

**6 个评分维度**（加权计入综合分）：

| 维度 | 检测内容 |
|------|---------|
| **触发率** | skill 实际被调用的频率 vs. 应该被调用的频率 |
| **用户反应** | 用户在 skill 触发后是接受、纠正还是拒绝？ |
| **Workflow 完成率** | skill 定义的步骤执行到了第几步？ |
| **静态质量** | 14 项检查：YAML 安全、CSO 合规、信息位置、字数等 |
| **漏触发** | 错失机会 — 用户需要但 skill 未被调用 |
| **Token 经济性** | 性价比和渐进式加载分层合规性 |

**3 个定性维度**（报告但不计分）：

| 维度 | 检测内容 |
|------|---------|
| **误触发** | 假阳性 — skill 触发了但用户不需要 |
| **跨 Skill 冲突** | 触发关键词重叠和 skill 间矛盾指导 |
| **环境一致性** | 文件路径失效、CLI 工具缺失、目录不存在 |

## 安装

复制下面的指令，直接粘贴到你的 agent 对话中即可自动安装：

### Claude Code

```
Install the skill-optimizer skill from https://github.com/hqhq1025/skill-optimizer
```

### Codex

```
Install the skill-optimizer skill from https://github.com/hqhq1025/skill-optimizer into ~/.codex/skills/
```

### 其他 Agent（Cursor、OpenCode、Gemini CLI 等）

```
Install the skill-optimizer skill from https://github.com/hqhq1025/skill-optimizer into ~/.agents/skills/
```

<details>
<summary>手动安装</summary>

```bash
# Claude Code
git clone https://github.com/hqhq1025/skill-optimizer.git /tmp/skill-optimizer
cp -r /tmp/skill-optimizer/skills/skill-optimizer ~/.claude/skills/
rm -rf /tmp/skill-optimizer

# Codex
git clone https://github.com/hqhq1025/skill-optimizer.git /tmp/skill-optimizer
cp -r /tmp/skill-optimizer/skills/skill-optimizer ~/.codex/skills/
rm -rf /tmp/skill-optimizer

# 共享路径（任意 agent）
git clone https://github.com/hqhq1025/skill-optimizer.git /tmp/skill-optimizer
cp -r /tmp/skill-optimizer/skills/skill-optimizer ~/.agents/skills/
rm -rf /tmp/skill-optimizer
```

</details>

## 使用

```
/optimize-skill              # 扫描所有 skills
/optimize-skill my-skill     # 单个 skill
/optimize-skill skill-a skill-b  # 多个 skills
```

生成的诊断报告包含：
- **总览表** — 所有 skill 一览，含评分
- **P0 修复** — 必须解决的阻断性问题
- **P1 改进** — 体验提升建议
- **P2 优化** — 可选微调
- **逐 Skill 诊断** — 每个 skill 的全部 8 个维度详情

## 多平台 Session 分析

自动检测可用平台并扫描所有 session 数据：

| 平台 | Skills 路径 | Session 数据路径 |
|------|------------|-----------------|
| Claude Code | `~/.claude/skills/` | `~/.claude/projects/**/*.jsonl` |
| Codex | `~/.codex/skills/` | `~/.codex/sessions/**/*.jsonl` |
| 共享 | `~/.agents/skills/` | — |

## 研究背景

分析维度基于同行评审的学术研究：

| 研究 | 关键发现 | 应用于 |
|------|---------|--------|
| [Memento-Skills](https://arxiv.org/abs/2603.18743) (2026) | Skills 作为结构化文件需要准确的路由；未被路由的 skill 无法通过读写学习循环自我改进 | 漏触发检测、复合风险评估 |
| [MCP Description Quality](https://arxiv.org/abs/2602.18914) (2026) | 好描述达到 72% 工具选择率 vs. 20% 随机基线（3.6 倍提升） | Description 质量检查、基于证据的改写建议 |
| [Lost in the Middle](https://arxiv.org/abs/2307.03172) (Liu et al., TACL 2024) | LLM 注意力呈 U 型曲线 — 中间内容被忽略 | 关键信息位置检查 |
| [Prompt Format Impact](https://arxiv.org/abs/2411.10541) (He et al., 2024) | 仅改格式就能带来 9-40% 的性能差异 | 静态质量分析 |
| [IFEval](https://arxiv.org/abs/2311.07911) (Zhou et al., 2023) | LLM 难以处理多约束提示 | 触发条件数量检查 |
| [Meincke et al.](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=5357179) (2025) | 说服性指令在不同模型间效果不一致 | MUST/NEVER 密度指导 |

## 工作原理

```
识别目标 skills（扫描 ~/.claude/skills/、~/.codex/skills/、~/.agents/skills/）
       ↓
采集 session 数据（自动检测平台，扫描 JSONL 记录）
       ↓
运行 8 个分析维度（6 个评分 + 3 个定性）
       ↓
计算综合评分（6 个评分维度的加权平均）
       ↓
输出 P0/P1/P2 优先级修复报告
```

**评分维度（加权平均）：**
- 触发率：25%
- 用户反应：20%
- Workflow 完成率：15%
- 静态质量：15%
- 漏触发：15%
- Token 经济性：10%

**定性维度**（误触发、跨 skill 冲突、环境一致性）以示例形式报告，不影响数值评分。

## 兼容性

兼容所有支持 [Agent Skills](https://agentskills.io) 开放标准的 agent：
- Claude Code
- Codex
- Cursor
- OpenCode
- Gemini CLI

## 许可证

MIT
