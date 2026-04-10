---
name: code-reader-skill
description: Multi-agent repository reading and knowledge-base synthesis for medium-to-large codebases. Use when Codex needs to read a repository end-to-end, split the work across subagents by system, domain, service, or layer, keep terminology and output structure consistent, and produce structured architecture and feature documentation for onboarding, handoff, or internal knowledge bases. This skill is especially useful for large repositories where parallel reading and staged synthesis are needed.
---

# Code Reader Skill

## Overview

Read a repository as an engineering handoff task, not as a shallow summary task. Build an evidence-backed understanding of systems, features, flows, configuration, and risks, then turn that understanding into documentation another engineer can use.

Treat the current thread as the total-control agent. When the user explicitly asks for multi-agent, delegation, or parallel work, spawn:

- one consistency-control subagent to normalize structure, terminology, evidence rules, and confidence labels
- multiple system-reader subagents to read disjoint parts of the repository in parallel

If the user did not explicitly ask for subagents, keep the same workflow but execute it in a single thread.

## Workflow

### 1. Confirm Execution Parameters

Before reading the repository in depth, ask the user short questions about:

- what language to use for the documentation
- where to store the generated documentation
- whether to enable multi-agent parallel reading

Also ask whether any repository areas should be excluded from reading or documentation. Accept exclusions such as:

- directories
- files
- services or subsystems
- generated code
- tests
- vendor or dependency folders
- infrastructure or deployment folders
- any user-defined scope exclusions

Default assumptions if the user does not specify them:

- documentation language: Chinese
- output directory: `/docs` under the repository root
- multi-agent parallel reading: enabled
- exclusions: none

Treat these as execution parameters for the whole task and keep them consistent across all agent outputs.

If multi-agent parallel reading is enabled, require Codex to use multi-agent parallel reading for the repository task:

- keep the current thread as the total-control agent
- spawn one consistency-control subagent
- spawn multiple system-reader subagents with disjoint scopes whenever the repository is large enough to benefit

If the user explicitly disables multi-agent parallel reading, keep the same documentation workflow but execute it in a single thread without spawning subagents.

### 2. Scan Before Splitting

Read enough of the repository to choose a safe split:

- top-level tree
- dependency files such as `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`
- runtime entrypoints
- build, test, deploy, and CI files
- major configuration directories

Use this scan to identify:

- technology stack
- runtime boundaries
- service or subsystem boundaries
- shared libraries and cross-cutting infrastructure
- likely P0 paths that deserve deeper reading first

Do not delegate before you understand the split well enough to avoid overlap.

Turn this pre-reading scan into visible documentation artifacts. Before any deep subsystem reading starts, write markdown files for the user that capture:

- repository index
- top-level structure
- detected technology stack and runtime boundaries
- proposed subsystem split
- subagent assignment plan
- reading priority plan
- explicit excluded scope

These files are not optional. The user must be able to inspect the repository structure and the proposed split before or while the deeper reading proceeds.

### 3. Define the Agent Roles

Use these roles consistently:

- Total-control agent:
  own the plan, repository split, progress tracking, gap analysis, and final synthesis
- Consistency-control agent:
  own the documentation contract, naming rules, confidence labels, and cross-subagent normalization
- System-reader subagents:
  each own one clearly bounded subsystem, service, layer, or business domain

Prefer disjoint ownership. A subagent may reference shared code, but one agent should still own the shared subsystem writeup.

Document the role plan in markdown as part of the pre-reading artifacts so the user can see:

- which agent owns orchestration
- which agent owns consistency review
- which subagents own which subsystem scopes
- where scope boundaries may need coordination

All agents must treat excluded scope as out of bounds unless the user later changes that decision.

### 4. Ask the Consistency-Control Agent for the Contract First

Before starting the system readers, have the consistency-control agent define and enforce:

- a single system writeup template
- a single feature writeup template
- a single call-chain format
- a single terminology map
- confidence labels:
  `[fact]`, `[inference]`, `[needs-confirmation]`
- citation rule:
  attach concrete file paths to important claims
- risk grading:
  `high`, `medium`, `low`

Keep the contract short. The goal is consistency, not bureaucracy.

Make sure the contract also states:

- the chosen documentation language
- the output directory for final artifacts
- the required pre-reading artifact filenames and their purpose
- the excluded scope and how to handle references to excluded content

### 5. Split the Repository Pragmatically

Choose one split strategy and state it explicitly:

- by service or deployable
- by business domain
- by frontend, backend, infra, data, tooling
- by runtime boundary

For very large repositories, use staged splitting:

1. repository-level scan
2. subsystem-level scan
3. module-level deep reading
4. feature and call-chain deep reading

Prioritize:

- P0: main user-facing or revenue-critical paths
- P1: supporting systems and shared components
- P2: tooling, scripts, and edge modules

Write the split decision down as markdown, including:

- why this split strategy was chosen
- what each subsystem boundary includes
- what shared code is intentionally handled separately
- what cross-system dependencies require later synthesis

### 6. Delegate with Bounded Prompts

When spawning system-reader subagents, give each agent:

- a precise ownership boundary
- the questions it must answer
- the output template it must follow
- the instruction to cite file paths
- the instruction to separate facts from inferences

Do not ask subagents to produce the final repository-wide document. Ask them to produce subsystem writeups that the total-control agent can merge.

Use the template in [references/system-template.md](./references/system-template.md).

Every system-reader subagent must read the pre-reading artifacts first so it works from the same repository map, subsystem split, and scope definition.

Every system-reader subagent must also respect the exclusion list. Do not read excluded files or directories in detail, and do not expand into excluded systems unless the user explicitly overrides the exclusion.

### 7. Read Features Through Real Code Paths

For each subsystem, prefer real execution paths over prose:

- entrypoint -> routing or dispatch -> service or domain layer -> storage or side effects -> returned result

Track:

- main responsibilities
- key APIs and interfaces
- configuration and environment variables
- state transitions and data models
- dependencies on other subsystems
- tests that confirm intended behavior
- mismatches between docs and code

For important features, document implementation details at a deeper level:

- the concrete entrypoints, handlers, classes, and functions involved
- what each stage of the flow is doing
- where inputs are validated and where branching decisions happen
- how payloads, domain objects, or persisted state change across the flow
- what side effects occur, such as writes, remote calls, emitted events, cache updates, or scheduled jobs
- what error paths, retries, permission checks, or consistency safeguards exist

If a claim depends only on naming or comments, mark it as `[inference]` unless code confirms it.

### 8. Reconcile Cross-System Knowledge

After subsystem writeups arrive, reconcile:

- duplicate subsystem names
- conflicting terminology
- incompatible granularity
- cross-system flows that no single reader could see in isolation
- shared infra or platform modules that multiple readers touched

Send targeted follow-up questions to subagents when needed. Avoid rereading everything from scratch if the gap is narrow and well scoped.

### 9. Produce Documentation as a Knowledge Artifact

The final output should read like internal engineering documentation, not raw notes. Use the structure in [references/final-doc-template.md](./references/final-doc-template.md).

Write the documents in the user-selected language and place them in the user-selected directory. If the user did not specify either parameter, use Chinese and `/docs` under the repository root.

In addition to the final consolidated documentation, always generate markdown files for the pre-reading phase. At minimum include:

- `00-repository-index.md`
- `01-structure-and-stack.md`
- `02-system-split-plan.md`
- `03-agent-assignment-plan.md`

These files should be created before deep subsystem reading, then updated if the understanding changes materially.

For all later documents, keep numeric prefixes for stable ordering, but do not hard-code number ranges to fixed categories such as frontend, backend, or infra. Assign numbers based on the repository's actual reading order and system decomposition after the initial scan.

The numbering rule is:

- earlier numbers for earlier reading and earlier user-facing context
- later numbers for later deep dives and synthesis artifacts
- one numbering scheme for the whole documentation set

Choose the sequence after the repository scan, write it down in the planning artifacts, and keep it consistent for all subsequent documents.

When exclusions exist, do not assign subagents to excluded scope. If excluded content is referenced by included systems, mention the dependency only at a high level and mark it as excluded rather than reading it in detail.

After all subsystem writeups are complete and the final consolidated documentation has been assembled, generate one additional markdown file for executive-level and onboarding-level overview:

- `99-overall-summary.md`

This file should provide a concise but complete cross-document overview of:

- what the repository is for
- how it is split into systems
- what the most important feature and runtime flows are
- what the key operational and architectural risks are
- what a new engineer should read first

Treat this as a top-down guide to the entire documentation set, not as a replacement for the detailed final document.

At minimum, cover:

- repository overview
- technology stack and runtime model
- subsystem map
- subsystem deep dives
- important features and end-to-end flows
- configuration model
- data model or core entities
- build, test, release, and deployment flow
- external integrations
- risks, technical debt, and hidden constraints
- open questions and suggested human verification
- onboarding path for a new engineer

## Delegation Guidance

### Consistency-Control Agent Prompt Pattern

Use a prompt like:

```text
You are the consistency-control agent for a repository-reading task. Define the required output contract for all subsystem writeups: section structure, terminology rules, confidence labels, citation expectations, call-chain format, and risk grading. Keep it concise and operational. Do not read the whole repository; focus on normalization and review criteria.
```

This agent should review subagent outputs and point out:

- missing sections
- unsupported claims
- vague descriptions
- inconsistent names
- feature and system levels being mixed together

### System-Reader Prompt Pattern

Use a prompt like:

```text
You are the reader for the assigned subsystem only. Read the code in your scope, identify the subsystem boundary, responsibilities, entrypoints, dependencies, feature flows, configuration, data structures, tests, risks, and open questions. For each important feature, explain the implementation in enough detail that another engineer can follow the real code path, including key functions or classes, step-by-step flow responsibilities, data changes, side effects, and error paths. Follow the provided template exactly. Cite concrete file paths. Mark every uncertain statement as [inference] or [needs-confirmation].
```

Assign each reader a disjoint scope. Explicitly tell it not to rewrite or reinterpret other subsystem boundaries unless the assigned scope requires noting a dependency.

## Quality Bar

Reject or revise subsystem writeups that:

- only restate file names without explaining behavior
- summarize README text without validating code
- omit key entrypoints or call chains
- describe features only at a surface level without enough implementation detail
- blur facts and guesses
- fail to cite evidence
- ignore tests, configuration, or external integrations when they materially affect behavior

## Output Rules

- Prefer concise, high-signal prose.
- Cite concrete file paths for important claims.
- State assumptions explicitly.
- Use `[fact]`, `[inference]`, and `[needs-confirmation]`.
- Point out doc-code mismatches.
- Keep subsystem and feature sections separate.
- For large repositories, deliver in phases, but keep all phase outputs mergeable into one final document.
- Keep pre-reading artifacts separate from subsystem deep dives and the final merged document.
- Keep the top-down overview summary separate from both the detailed final document and the pre-reading artifacts.
- If exclusions exist, state them clearly in the planning docs and do not silently read excluded scope.

## Completion Checklist

- repository split is explicit
- consistency contract exists
- each important subsystem has an owner
- core user or business flows are traced through code
- shared infrastructure is documented once at the correct level
- pre-reading artifacts for index, structure, split, and agent plan exist as markdown files
- final document merges subsystem writeups into one coherent knowledge base
- top-down overview summary exists as a separate markdown file after all detailed work is complete
- unresolved questions are listed separately from confirmed findings
- user-specified exclusions are documented and respected throughout the workflow
