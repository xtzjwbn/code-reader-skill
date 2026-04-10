# Pre-Reading Artifact Templates

Generate these markdown files before deep subsystem reading starts. They give the user and all subagents a shared repository map.

## 00-repository-index.md

Include:

- repository name or inferred purpose
- top-level directories and files
- important entrypoints
- important config, build, test, and deployment files
- a quick list of likely high-value reading targets
- explicit exclusions provided by the user

## 01-structure-and-stack.md

Include:

- detected languages and frameworks
- package and dependency systems
- runtime model
- deployables, services, apps, workers, libraries, or tooling layers
- important shared infrastructure

## 02-system-split-plan.md

Include:

- chosen split strategy
- subsystem list
- boundary definition for each subsystem
- rationale for the split
- known overlaps or ambiguity
- priority level for each subsystem
- excluded scope that should not be assigned for deep reading

## 03-agent-assignment-plan.md

Include:

- total-control agent responsibilities
- consistency-control agent responsibilities
- one section per system-reader subagent
- owned paths or scopes for each subagent
- explicit questions each subagent must answer
- expected handoff format

Also include the planned document ordering and filenames for the next phase. Do not bind number ranges to fixed categories in advance. Instead, choose numbering based on the actual repository reading order and subsystem split discovered during the scan.

## Rules

- Keep these files concise but concrete.
- Cite file paths where relevant.
- Update them if the repository map changes during deeper reading.
- Require subagents to read them before producing subsystem writeups.
- Keep the numbering scheme stable once assigned so later documents do not drift.
- Record excluded paths or systems early so subagents can avoid them.
