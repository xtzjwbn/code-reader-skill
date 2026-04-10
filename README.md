# code-reader-skill

`code-reader-skill` is a Codex skill for reading medium-to-large repositories with a structured multi-agent workflow. It helps Codex scan a repository, create a visible reading plan, split the work across subagents, keep documentation output consistent, and produce a full set of engineering knowledge documents.

`code-reader-skill` 是一个面向 Codex 的技能，用于以结构化多 agent 工作流阅读中大型仓库。它帮助 Codex 扫描仓库、生成可见的阅读计划、将工作拆分给 subagent、统一文档输出，并产出一整套工程知识文档。

## What It Does | 能力概览

- Reads a repository as an engineering handoff task instead of a shallow summary.
- Supports a total-control agent, a consistency-control agent, and multiple system-reader subagents.
- Generates pre-reading markdown artifacts before deep reading starts.
- Lets the user choose documentation language and output directory.
- Asks whether multi-agent parallel reading should be enabled and defaults to enabled.
- Lets the user exclude directories, files, systems, or other scopes from reading.
- Uses evidence-backed subsystem writeups and final merged documentation.
- Requires subsystem feature descriptions to explain implementation details, not just feature names and responsibilities.
- Produces a final top-down overview summary after all detailed work is complete.

- 将仓库阅读任务按“工程交接”标准执行，而不是停留在表层总结。
- 支持总控 agent、一致性控制 agent 和多个系统阅读 subagent 协作。
- 在深度阅读前先生成前期扫描与规划文档。
- 支持用户指定文档语言和输出目录。
- 会询问是否开启多 agent 并行阅读，默认开启。
- 支持用户排除不希望阅读的目录、文件、系统或其他范围。
- 基于代码证据生成子系统文档与最终整合文档。
- 要求对子系统中的重要功能写出更细的实现说明，而不只是功能名称和职责概述。
- 在所有细节文档完成后，再生成一份顶层纵览总结。

## Workflow | 工作流

1. Confirm documentation language, output directory, whether multi-agent parallel reading is enabled, and excluded scope.
2. Scan the repository and generate pre-reading artifacts.
3. Define the repository split and subagent assignment plan.
4. Ask a consistency-control agent to define the output contract.
5. Let system-reader subagents read only their assigned scopes.
6. Reconcile cross-system knowledge and produce detailed documentation.
7. Merge everything into a final repository document.
8. Write a separate top-down summary document for fast global review.

1. 确认文档语言、输出目录、是否开启多 agent 并行阅读，以及排除范围。
2. 扫描仓库并生成前期扫描文档。
3. 制定系统拆分方案和 subagent 分工方案。
4. 由一致性控制 agent 制定统一输出规范。
5. 让系统阅读 subagent 只阅读各自负责的范围。
6. 统一跨系统知识并产出详细文档。
7. 整合为最终仓库知识文档。
8. 额外生成一份适合快速纵览的总览文档。

## Default Behavior | 默认行为

- Documentation language defaults to Chinese.
- Documentation output directory defaults to `/docs` under the repository root.
- Multi-agent parallel reading defaults to enabled.
- Exclusions default to `none`.
- Important claims should be marked as `[fact]`, `[inference]`, or `[needs-confirmation]`.
- Numbered filenames are kept in stable order, but later numbering is assigned by AI according to the repository's actual reading order rather than fixed category ranges.

- 文档语言默认是中文。
- 文档输出目录默认是仓库根目录下的 `/docs`。
- 多 agent 并行阅读默认开启。
- 默认没有排除范围。
- 重要结论应标记为 `[fact]`、`[inference]` 或 `[needs-confirmation]`。
- 文档文件名保留顺序编号，但后续编号不绑定固定类别，而是由 AI 根据仓库实际阅读顺序自主分配。

## Documentation Outputs | 文档产物

The skill requires multiple layers of documents.

该技能要求生成多层级文档。

### Pre-reading Artifacts | 前期扫描文档

Generated before deep subsystem reading:

在子系统深读前生成：

- `00-repository-index.md`
- `01-structure-and-stack.md`
- `02-system-split-plan.md`
- `03-agent-assignment-plan.md`

These documents make the repository map, split strategy, and subagent scopes visible to both the user and the subagents.

这些文档用于向用户和 subagent 明确展示仓库索引、结构理解、拆分方案和分工范围。

### Deep Reading Documents | 深度阅读文档

Later numbered markdown files are assigned according to the actual repository reading order and subsystem decomposition. They cover subsystem writeups, cross-system flows, and other detailed outputs.

后续编号文档由 AI 根据真实阅读顺序和系统拆分结果分配，用于承载各子系统说明、跨系统链路和其他详细产物。

For important subsystem features, the writeup should include concrete implementation detail such as entrypoints, key functions or classes, step-by-step flow responsibilities, data changes, side effects, and error paths.

对于重要子系统功能，文档应包含更具体的实现细节，例如入口、关键函数或类、分步处理职责、数据变化、副作用和错误路径。

### Final Outputs | 最终产物

- A merged final repository document based on all subsystem writeups
- `99-overall-summary.md` for top-down cross-document review

- 基于所有子系统文档整合出的最终仓库知识文档
- `99-overall-summary.md`，用于对整套文档做顶层纵览

## Exclusions | 排除机制

Before deep reading starts, the skill asks whether any content should be excluded. Exclusions can include:

- directories
- files
- services or subsystems
- generated code
- tests
- vendor or dependency folders
- infra or deployment folders
- any other user-defined scope

在开始深度阅读前，技能会询问是否有需要排除的内容。排除项可以包括：

- 目录
- 文件
- 服务或子系统
- 生成代码
- 测试
- 依赖目录
- 基础设施或部署目录
- 任何用户自定义范围

Excluded scope is recorded in the planning docs and must be respected by all subagents.

排除范围会记录在前期规划文档中，并且所有 subagent 都必须遵守。

## Repository Structure | 当前仓库结构

```text
.
├── SKILL.md
├── README.md
├── agents/
│   └── openai.yaml
└── references/
    ├── final-doc-template.md
    ├── overall-summary-template.md
    ├── pre-reading-artifacts.md
    └── system-template.md
```

## Key Files | 核心文件

- `SKILL.md`: Main workflow and delegation rules.
- `agents/openai.yaml`: UI metadata for the skill.
- `references/pre-reading-artifacts.md`: Templates for scan and planning documents.
- `references/system-template.md`: Required template for subsystem writeups.
- `references/final-doc-template.md`: Template for the merged final repository document.
- `references/overall-summary-template.md`: Template for the final top-down overview summary.

- `SKILL.md`：主流程与多 agent 协作规则。
- `agents/openai.yaml`：该技能的界面元数据。
- `references/pre-reading-artifacts.md`：前期扫描与规划文档模板。
- `references/system-template.md`：子系统文档模板。
- `references/final-doc-template.md`：最终整合文档模板。
- `references/overall-summary-template.md`：最终顶层总览文档模板。

## Example Usage | 使用示例

```text
$code-reader-skill Please read this repository with a total-control agent, a consistency-control agent, and subsystem readers. Ask for documentation language, output directory, whether multi-agent parallel reading should stay enabled, and excluded scope first. Then generate the pre-reading markdown files, assign subagents, and produce the full documentation set.
```

```text
$code-reader-skill 请用总控 agent、一致性控制 agent 和系统阅读 subagent 完整阅读当前仓库。先确认文档语言、输出目录、是否开启多 agent 并行阅读，以及排除范围，再生成前期扫描文档、分配 subagent，并完成整套知识文档。
```

## Installation | 安装

This repository contains the local source for the skill. To make Codex discover it automatically, install it into:

这个仓库保存的是该技能的本地源码。若要让 Codex 自动发现它，需要安装到：

```text
~/.codex/skills/code-reader-skill
```

After installation, restart Codex to pick up the skill.

安装后需要重启 Codex 才能识别该技能。
