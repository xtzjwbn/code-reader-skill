# code-reader-skill

`code-reader-skill` is a Codex skill for reading medium-to-large repositories with a structured multi-agent workflow. It helps Codex scan a repository, create a visible reading plan, split the work across subagents, keep documentation output consistent, and produce a full set of engineering knowledge documents.

`code-reader-skill` 是一个面向 Codex 的技能，用于以结构化多 agent 工作流阅读中大型仓库。它帮助 Codex 扫描仓库、生成可见的阅读计划、将工作拆分给 subagent、统一文档输出，并产出一整套工程知识文档。

## What It Does | 能力概览

- Reads a repository as an engineering handoff task instead of a shallow summary.
- Supports a total-control agent, a consistency-control agent, and multiple system-reader subagents.
- Generates pre-reading markdown artifacts before deep reading starts.
- Detects whether similar repository documentation already exists before deep reading starts.
- Lets the user choose documentation language and output directory.
- Asks whether multi-agent parallel reading should be enabled and defaults to enabled.
- Lets the user exclude directories, files, systems, or other scopes from reading.
- If similar documents exist, asks whether to reuse them, continue writing in them, or rebuild from scratch.
- Uses evidence-backed subsystem writeups and final merged documentation.
- Requires subsystem feature descriptions to explain implementation details, not just feature names and responsibilities.
- Requires each subsystem document to include a keyword index that maps grep-friendly terms to code locations and logic.
- Allows multiple synonyms or aliases to point to the same keyword explanation so grep lookups converge on one canonical entry.
- Supports targeted updates to existing documents: a user can name a keyword and the skill will read code, enrich that keyword entry, and write the result back into the relevant document.
- Produces a final top-down overview summary after all detailed work is complete.

- 将仓库阅读任务按“工程交接”标准执行，而不是停留在表层总结。
- 支持总控 agent、一致性控制 agent 和多个系统阅读 subagent 协作。
- 在深度阅读前先生成前期扫描与规划文档。
- 会先检测仓库中是否已经存在类似知识文档。
- 支持用户指定文档语言和输出目录。
- 会询问是否开启多 agent 并行阅读，默认开启。
- 支持用户排除不希望阅读的目录、文件、系统或其他范围。
- 如果已存在类似文档，会反问用户是复用、续写，还是重建。
- 基于代码证据生成子系统文档与最终整合文档。
- 要求对子系统中的重要功能写出更细的实现说明，而不只是功能名称和职责概述。
- 要求每个子系统文档都包含关键词索引，帮助用户通过 grep 关键词快速定位代码和对应逻辑说明。
- 允许多个同义词或别名指向同一个关键词解释，避免同一概念重复写多份说明。
- 支持对已有文档做定点补写：用户可以指定某个关键词，skill 会阅读相关代码并把这个关键词对应的信息补进文档。
- 在所有细节文档完成后，再生成一份顶层纵览总结。

## Workflow | 工作流

1. Confirm documentation language, output directory, whether multi-agent parallel reading is enabled, whether existing-document detection is enabled, and excluded scope.
2. Scan the repository, detect existing related documents, and generate pre-reading artifacts.
3. If similar documents are found, ask whether to reuse, continue, or rebuild.
4. Define the repository split and subagent assignment plan.
5. Ask a consistency-control agent to define the output contract.
6. Let system-reader subagents validate code, and when applicable, compare existing documents against code before trusting them.
7. Reconcile cross-system knowledge and produce detailed documentation.
8. Merge everything into a final repository document.
9. Write a separate top-down summary document for fast global review.

1. 确认文档语言、输出目录、是否开启多 agent 并行阅读、是否开启已有文档检测，以及排除范围。
2. 扫描仓库、检测已有相关文档，并生成前期扫描文档。
3. 如果发现类似文档，先反问用户是复用、续写还是重建。
4. 制定系统拆分方案和 subagent 分工方案。
5. 由一致性控制 agent 制定统一输出规范。
6. 让系统阅读 subagent 在读代码时验证已有文档，不能直接信任旧文档。
7. 统一跨系统知识并产出详细文档。
8. 整合为最终仓库知识文档。
9. 额外生成一份适合快速纵览的总览文档。

## Default Behavior | 默认行为

- Documentation language defaults to Chinese.
- Documentation output directory defaults to `/docs` under the repository root.
- Multi-agent parallel reading defaults to enabled.
- Existing-document detection defaults to enabled.
- Exclusions default to `none`.
- Important claims should be marked as `[fact]`, `[inference]`, or `[needs-confirmation]`.
- Numbered filenames are kept in stable order, but later numbering is assigned by AI according to the repository's actual reading order rather than fixed category ranges.
- Similar existing documents are matched by topic coverage first, with filenames and directories used only as supporting signals.
- If existing documents are found, the workflow asks whether to `复用`, `续写`, or `重建`.
- If the user chooses `复用` or `续写`, the default behavior is to update the existing document in place.
- Even when old documents exist, code remains the source of truth.

- 文档语言默认是中文。
- 文档输出目录默认是仓库根目录下的 `/docs`。
- 多 agent 并行阅读默认开启。
- 已有文档检测默认开启。
- 默认没有排除范围。
- 重要结论应标记为 `[fact]`、`[inference]` 或 `[needs-confirmation]`。
- 文档文件名保留顺序编号，但后续编号不绑定固定类别，而是由 AI 根据仓库实际阅读顺序自主分配。
- 类似文档默认按主题覆盖范围匹配，文件名和目录只作为辅助信号。
- 如果检测到已有文档，流程会反问用户选择 `复用`、`续写` 或 `重建`。
- 如果用户选择 `复用` 或 `续写`，默认原地更新已有文档。
- 即使已有文档存在，代码仍然是最终事实来源。

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
- `04-existing-docs-inventory.md` when existing-document detection is enabled

These documents make the repository map, split strategy, and subagent scopes visible to both the user and the subagents.

这些文档用于向用户和 subagent 明确展示仓库索引、结构理解、拆分方案和分工范围。

When similar documents already exist, the workflow also creates an inventory document that maps existing files to target topics and records the chosen handling mode.

当仓库中已经存在类似文档时，流程还会生成一份已有文档清单，用来映射旧文档与目标主题，并记录最终选择的处理方式。

### Deep Reading Documents | 深度阅读文档

Later numbered markdown files are assigned according to the actual repository reading order and subsystem decomposition. They cover subsystem writeups, cross-system flows, and other detailed outputs.

后续编号文档由 AI 根据真实阅读顺序和系统拆分结果分配，用于承载各子系统说明、跨系统链路和其他详细产物。

For important subsystem features, the writeup should include concrete implementation detail such as entrypoints, key functions or classes, step-by-step flow responsibilities, data changes, side effects, and error paths.

对于重要子系统功能，文档应包含更具体的实现细节，例如入口、关键函数或类、分步处理职责、数据变化、副作用和错误路径。

When existing documentation is used, subsystem writeups must also record what the old documents claimed, what the code confirmed, what the old documents got wrong, and what the old documents missed.

当工作流使用了已有文档时，子系统文档还必须记录旧文档声称了什么、代码验证了什么、旧文档哪里写错了，以及旧文档漏掉了什么。

Each subsystem writeup also includes a keyword index so a user can grep an identifier, term, config key, job name, event name, or model name and quickly find the corresponding explanation in the docs.

每个子系统文档还需要包含关键词索引，这样用户 grep 某个标识符、术语、配置键、任务名、事件名或模型名时，可以快速在文档里找到对应说明。

Multiple grep terms can map to one canonical keyword entry when they refer to the same concept.

如果多个 grep 词实际指向同一个概念，它们可以统一映射到同一个规范关键词条目。

The same keyword mechanism also supports targeted enrichment of existing documents: if the user asks for a specific keyword, the skill reads the relevant code and updates the matching document entry with newly verified details.

同一套关键词机制也支持对已有文档做定点增强：如果用户指定某个关键词，skill 会阅读相关代码，并把新验证到的细节补进对应文档条目。

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

## Existing Documents | 已有文档处理

If existing related documents are detected, the workflow does not silently reuse them. It pauses and asks the user to choose one of three modes:

- `复用`: keep the old document as the base and revise it in place using code evidence
- `续写`: keep the old document and continue writing missing parts in place, while correcting clearly wrong content
- `重建`: read the old document only as supporting context and generate a fresh standardized output set

如果检测到已有相关文档，流程不会静默复用，而是先停下来让用户在三种模式中选择：

- `复用`：保留旧文档作为基础，并基于代码证据原地修订
- `续写`：保留旧文档，并在原地继续补全缺失内容，同时修正明显错误
- `重建`：旧文档只作为辅助参考，重新生成一套标准化文档

## Repository Structure | 当前仓库结构

```text
.
├── SKILL.md
├── README.md
├── agents/
│   └── openai.yaml
└── references/
    ├── existing-docs-inventory-template.md
    ├── final-doc-template.md
    ├── overall-summary-template.md
    ├── pre-reading-artifacts.md
    └── system-template.md
```

## Key Files | 核心文件

- `SKILL.md`: Main workflow and delegation rules.
- `agents/openai.yaml`: UI metadata for the skill.
- `references/pre-reading-artifacts.md`: Templates for scan and planning documents.
- `references/existing-docs-inventory-template.md`: Template for detecting and mapping existing repository documents.
- `references/system-template.md`: Required template for subsystem writeups.
- `references/final-doc-template.md`: Template for the merged final repository document.
- `references/overall-summary-template.md`: Template for the final top-down overview summary.

- `SKILL.md`：主流程与多 agent 协作规则。
- `agents/openai.yaml`：该技能的界面元数据。
- `references/pre-reading-artifacts.md`：前期扫描与规划文档模板。
- `references/existing-docs-inventory-template.md`：已有文档检测与映射清单模板。
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
