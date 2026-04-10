# code-reader-skill

`code-reader-skill` is a Codex skill for reading medium-to-large repositories with a structured multi-agent workflow. It helps Codex split a codebase into systems, delegate bounded reading tasks to subagents, normalize terminology and evidence rules, and produce unified engineering documentation.

`code-reader-skill` 是一个面向 Codex 的技能，用于以结构化多 agent 工作流阅读中大型仓库。它帮助 Codex 拆分系统边界、将阅读任务分配给 subagent、统一术语和证据规范，并最终产出一致的工程知识文档。

## What It Does | 功能概览

- Reads a repository as an engineering handoff task instead of a shallow summary.
- Uses a total-control agent, a consistency-control agent, and multiple system-reader subagents.
- Supports staged reading for large repositories.
- Produces evidence-backed subsystem writeups and final repository documentation.
- Asks for documentation language and output directory before deep reading starts.

- 将仓库阅读任务按“工程交接”标准执行，而不是停留在表层总结。
- 使用总控 agent、一致性控制 agent 和多个系统阅读 subagent 协作。
- 支持超大仓库的分阶段阅读。
- 产出基于代码证据的子系统说明和最终仓库知识文档。
- 在开始深度阅读前，先确认文档语言和输出目录。

## Default Behavior | 默认行为

- Documentation language defaults to Chinese.
- Documentation output directory defaults to `/docs` under the repository root.
- Important claims should be marked as `[fact]`, `[inference]`, or `[needs-confirmation]`.

- 文档语言默认是中文。
- 文档输出目录默认是仓库根目录下的 `/docs`。
- 重要结论需要标记为 `[fact]`、`[inference]` 或 `[needs-confirmation]`。

## Repository Structure | 目录结构

```text
.
├── SKILL.md
├── README.md
├── agents/
│   └── openai.yaml
└── references/
    ├── final-doc-template.md
    └── system-template.md
```

## Key Files | 核心文件

- `SKILL.md`: Main workflow and delegation rules for multi-agent repository reading.
- `references/system-template.md`: Required output template for each subsystem reader.
- `references/final-doc-template.md`: Final merged documentation template.
- `agents/openai.yaml`: UI-facing metadata for the skill.

- `SKILL.md`：多 agent 仓库阅读流程与协作规则。
- `references/system-template.md`：每个系统阅读 subagent 的统一输出模板。
- `references/final-doc-template.md`：最终整合文档模板。
- `agents/openai.yaml`：该技能的界面元数据。

## Example Usage | 使用示例

```text
$code-reader-skill Please read this repository with a total-control agent, a consistency-control agent, and subsystem readers. First give the split plan, agent plan, documentation template, and reading plan, then execute.
```

```text
$code-reader-skill 请用总控 agent、一致性控制 agent 和系统阅读 subagent 来完整阅读当前仓库。先给出仓库拆分方案、agent 分工方案、文档模板和阅读计划，再开始执行。
```

## Notes | 说明

- This repository contains the local source for the skill.
- To make Codex discover it automatically, install it into `~/.codex/skills/code-reader-skill`.
- After installation, restart Codex to pick up the new skill.

- 这个仓库保存的是该技能的本地源码。
- 若要让 Codex 自动发现它，需要将其安装到 `~/.codex/skills/code-reader-skill`。
- 安装后需要重启 Codex 才能识别新技能。
