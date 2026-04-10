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
- whether to detect existing repository documentation before starting the main reading flow

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
- existing-document detection: enabled
- exclusions: none

Treat these as execution parameters for the whole task and keep them consistent across all agent outputs.

If the user is not asking for a full repository reading pass, check whether the request is a targeted documentation update instead. A targeted update is valid when the user wants an existing document to be enriched with information about a specific keyword, identifier, config key, event name, job name, model name, or other grep-friendly term.

Also check whether the request is a detail-document expansion from existing system docs. This task type is valid when the user wants a dedicated knowledge document for:

- a subsystem detail
- a specific feature inside an existing system
- an implementation detail
- a call chain
- a configuration topic
- a concept, exception path, state transition, or data flow that deserves its own focused document

Treat this as different from a keyword-only update:

- keyword update: enrich an existing keyword entry inside an existing document
- detail-document expansion: create a new standalone child document for the requested detail

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

If existing-document detection is enabled, also scan:

- the chosen output directory
- common repository documentation locations such as `docs/`, `doc/`, `documentation/`, `knowledge-base/`, `kb/`
- markdown files near major subsystems when they appear to document runtime behavior, architecture, or ownership

Use this scan to identify:

- technology stack
- runtime boundaries
- service or subsystem boundaries
- shared libraries and cross-cutting infrastructure
- likely P0 paths that deserve deeper reading first
- whether similar documentation already exists for major repository topics

Do not delegate before you understand the split well enough to avoid overlap.

Turn this pre-reading scan into visible documentation artifacts. Before any deep subsystem reading starts, write markdown files for the user that capture:

- repository index
- top-level structure
- detected technology stack and runtime boundaries
- proposed subsystem split
- subagent assignment plan
- reading priority plan
- explicit excluded scope
- existing-document inventory and topic mapping

These files are not optional. The user must be able to inspect the repository structure and the proposed split before or while the deeper reading proceeds.

When existing-document detection is enabled, treat existing documentation as discoverable input to the workflow, not as trusted truth. Code remains the source of truth.

If similar documentation is found for any important topic, pause and ask the user how to proceed before deep reading starts. The allowed handling modes are:

- `reuse`: keep the existing document as the base and revise it in place based on code validation
- `continue`: keep the existing document and continue writing missing or incomplete sections in place, while still correcting clearly wrong content using code evidence
- `rebuild`: ignore the old document as the output base, but allow it to be read as supporting context while generating a fresh standardized document set

When the user answers with localized terms, map them to these three handling modes before proceeding.

Do not assume a mode automatically when similar documentation exists, even if only part of the document set already exists.

If the user asked for a targeted keyword update to existing documentation, use the scan to identify:

- which existing document is the best target for the keyword
- which subsystem or cross-system topic the keyword appears to belong to
- which code areas should be read to validate and enrich the document

If the user asked for a detail-document expansion from existing system docs, use the scan to identify:

- the most relevant existing parent system document
- whether the existing documentation tree already has a subsystem-specific child folder structure
- which code areas should be read to produce the new child document
- whether the parent document already has a short section that should link to the new child document

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
- the existing-document handling mode chosen by the user, if applicable
- the rule that code evidence overrides stale or conflicting documentation

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
- which existing documents map to which subsystems or cross-system topics

### 6. Delegate with Bounded Prompts

When spawning system-reader subagents, give each agent:

- a precise ownership boundary
- the questions it must answer
- the output template it must follow
- the instruction to cite file paths
- the instruction to separate facts from inferences
- the instruction to compare any mapped existing documents against code before trusting them
- when applicable, the instruction to enrich an existing document for a specific keyword by reading code first
- when applicable, the instruction to create a new detail child document from an existing system document after reading both the document and the relevant code

Do not ask subagents to produce the final repository-wide document. Ask them to produce subsystem writeups that the total-control agent can merge.

Use the template in [references/system-template.md](./references/system-template.md).

Every system-reader subagent must read the pre-reading artifacts first so it works from the same repository map, subsystem split, and scope definition.

Every system-reader subagent must also respect the exclusion list. Do not read excluded files or directories in detail, and do not expand into excluded systems unless the user explicitly overrides the exclusion.

When a subsystem has mapped existing documentation and the user chose `reuse` or `continue`, the system-reader subagent must:

- read the mapped existing document first
- extract subsystem boundaries, feature claims, call-chain claims, configuration claims, and risk claims as initial hypotheses
- validate those hypotheses against code
- explicitly record matches, mismatches, omissions, and corrected conclusions

When the user chose `rebuild`, existing documentation may still be read as supporting context, but it must not define the target output structure.

When the user asks to add information for a specific keyword to an existing document, the responsible reader must:

- locate the most relevant existing document or subsystem document for that keyword
- read the existing keyword coverage, if any
- read the relevant code paths tied to that keyword
- update or append the keyword entry with code-backed details
- expand nearby feature descriptions when the keyword reveals missing implementation context

When the user asks for a detail-document expansion from an existing system document, the responsible reader must:

- locate the most relevant parent system document
- read the parent document to recover context, terminology, and current scope boundaries
- read the relevant code paths for the requested detail
- create a new standalone child document under the system-specific child folder
- update the parent system document with a link to the child document and a short summary
- preserve any existing brief explanation in the parent document unless the user explicitly asked for structural refactoring

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
- stable subsystem keywords that a user is likely to grep for in the codebase

For important features, document implementation details at a deeper level:

- the concrete entrypoints, handlers, classes, and functions involved
- what each stage of the flow is doing
- where inputs are validated and where branching decisions happen
- how payloads, domain objects, or persisted state change across the flow
- what side effects occur, such as writes, remote calls, emitted events, cache updates, or scheduled jobs
- what error paths, retries, permission checks, or consistency safeguards exist

If existing documentation described the feature already, compare the documented behavior against the actual code path and record:

- what the document claimed
- what the code confirmed
- what the document got wrong or omitted

For each subsystem, produce a keyword index section that helps a user jump from a grep result back into the documentation. For each important keyword, include:

- the canonical keyword
- synonyms, aliases, abbreviations, or other grep-friendly variants that should map to the same explanation
- why the keyword matters in this subsystem
- the most relevant files, classes, functions, handlers, or jobs tied to that keyword
- the logic or behavior the keyword maps to

When multiple terms point to the same underlying concept, prefer one canonical keyword entry with multiple synonyms instead of duplicating similar entries.

If the user explicitly requests a keyword to be added or expanded, prioritize that keyword even if it was not part of the original subsystem keyword set.

If the user explicitly requests a dedicated detail document, prioritize producing a focused child document over expanding the parent document inline.

If a claim depends only on naming or comments, mark it as `[inference]` unless code confirms it.

### 8. Reconcile Cross-System Knowledge

After subsystem writeups arrive, reconcile:

- duplicate subsystem names
- conflicting terminology
- incompatible granularity
- cross-system flows that no single reader could see in isolation
- shared infra or platform modules that multiple readers touched
- mismatches between existing documentation and verified code behavior
- keyword entries that point to weak or incomplete code explanations
- child detail documents that are not clearly linked back to their parent system documents

Send targeted follow-up questions to subagents when needed. Avoid rereading everything from scratch if the gap is narrow and well scoped.

### 9. Produce Documentation as a Knowledge Artifact

The final output should read like internal engineering documentation, not raw notes. Use the structure in [references/final-doc-template.md](./references/final-doc-template.md).

Write the documents in the user-selected language and place them in the user-selected directory. If the user did not specify either parameter, use Chinese and `/docs` under the repository root.

In addition to the final consolidated documentation, always generate markdown files for the pre-reading phase. At minimum include:

- `00-repository-index.md`
- `01-structure-and-stack.md`
- `02-system-split-plan.md`
- `03-agent-assignment-plan.md`
- `04-existing-docs-inventory.md` when existing-document detection is enabled

These files should be created before deep subsystem reading, then updated if the understanding changes materially.

Use [references/existing-docs-inventory-template.md](./references/existing-docs-inventory-template.md) for the existing-document inventory artifact.

For all later documents, keep numeric prefixes for stable ordering, but do not hard-code number ranges to fixed categories such as frontend, backend, or infra. Assign numbers based on the repository's actual reading order and system decomposition after the initial scan.

The numbering rule is:

- earlier numbers for earlier reading and earlier user-facing context
- later numbers for later deep dives and synthesis artifacts
- one numbering scheme for the whole documentation set

Choose the sequence after the repository scan, write it down in the planning artifacts, and keep it consistent for all subsequent documents.

When exclusions exist, do not assign subagents to excluded scope. If excluded content is referenced by included systems, mention the dependency only at a high level and mark it as excluded rather than reading it in detail.

If existing documentation is found, record for each relevant document:

- detected topic coverage
- mapped target document or subsystem
- apparent completeness: complete, partial, stale-suspected, or unclear
- user-selected handling mode

When the task is a detail-document expansion, record:

- requested detail topic
- chosen parent system document
- chosen child folder path
- new child document path
- whether the parent document was updated with a link and summary

When the task is a targeted keyword update, record:

- requested keyword
- target document chosen for update
- code areas read for enrichment
- whether the keyword entry was newly added or expanded

After all subsystem writeups are complete and the final consolidated documentation has been assembled, generate one additional markdown file for executive-level and onboarding-level overview:

- `99-overall-summary.md`

This file should provide a concise but complete cross-document overview of:

- what the repository is for
- how it is split into systems
- what the most important feature and runtime flows are
- what the key operational and architectural risks are
- what a new engineer should read first
- which important documents were newly created versus updated from existing materials

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
- existing-document claims that were accepted without code validation
- missing doc-code mismatch records when old documents were used
- missing or low-value subsystem keywords that do not actually help code lookup
- duplicate keyword entries that should have been merged under one canonical keyword with synonyms

### System-Reader Prompt Pattern

Use a prompt like:

```text
You are the reader for the assigned subsystem only. Read the code in your scope, identify the subsystem boundary, responsibilities, entrypoints, dependencies, feature flows, configuration, data structures, tests, risks, and open questions. For each important feature, explain the implementation in enough detail that another engineer can follow the real code path, including key functions or classes, step-by-step flow responsibilities, data changes, side effects, and error paths. Follow the provided template exactly. Cite concrete file paths. Mark every uncertain statement as [inference] or [needs-confirmation].
```

Also require the reader to extract a practical keyword index for the subsystem so users can grep a keyword in the repo and quickly find the matching documentation explanation.

If the user asked to add or expand a specific keyword in existing documentation, require the reader to update that keyword entry using code-backed evidence rather than only reorganizing existing prose.

If the user asked for a dedicated detail child document, require the reader to read the parent system document first, then read the relevant code, then produce the child document and parent-document backlink summary using code-backed evidence.

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
- reuse old documentation claims without validating them against code
- fail to distinguish documented claims from code-verified findings when existing documents were in scope
- omit a useful keyword index for the subsystem
- update a keyword entry without reading the relevant code paths
- fail to merge obvious synonym terms into a single keyword explanation
- create a detail child document without linking it back to the parent system document
- create a detail child document without reading both the parent document and the relevant code

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
- If existing documentation is found, record detection results and the chosen handling mode before deep reading starts.
- When existing documentation is used, make doc-code mismatches explicit instead of silently normalizing them away.
- Ensure each subsystem document includes a keyword index that supports grep-driven lookup.
- Allow multiple synonym grep terms to point to one canonical keyword explanation.
- For targeted keyword updates, update the keyword entry and surrounding logic explanation based on code evidence.
- For detail-document expansion requests, create a new child document under the parent system folder and update the parent document with a link and short summary.

## Completion Checklist

- repository split is explicit
- consistency contract exists
- each important subsystem has an owner
- core user or business flows are traced through code
- shared infrastructure is documented once at the correct level
- pre-reading artifacts for index, structure, split, and agent plan exist as markdown files
- existing-document inventory exists when document detection is enabled
- final document merges subsystem writeups into one coherent knowledge base
- top-down overview summary exists as a separate markdown file after all detailed work is complete
- unresolved questions are listed separately from confirmed findings
- user-specified exclusions are documented and respected throughout the workflow
- existing documents, when present, are either reused, continued, or rebuilt according to the user-selected mode
- each subsystem writeup includes actionable keywords mapped to code and logic
- keyword-specific update requests enrich existing documents with newly verified code-backed information
- detail-document expansion requests produce linked child documents under the system-specific documentation folder
