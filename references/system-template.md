# Subsystem Writeup Template

Use this exact structure for each system-reader subagent.

## 1. System Name and Boundary

- Name of the subsystem
- What it is responsible for
- What it explicitly does not own
- Where it sits in the repository and runtime

## 2. Key Paths

- Main directories
- Entrypoints
- Configuration files
- Test files
- Important scripts or generated artifacts

## 3. Keywords

List a practical keyword index for this subsystem. Prefer keywords a user would realistically grep for in the repository.

For each keyword:

- canonical keyword
- synonyms, aliases, abbreviations, or related grep terms that should point to the same explanation
- why it matters
- the main files, classes, functions, handlers, jobs, config keys, or schema names tied to it
- the logic or behavior it maps to
- whether this keyword entry is newly added, expanded, or unchanged when updating an existing document

Use one canonical keyword entry when multiple grep terms refer to the same concept. Group those terms together instead of duplicating the explanation across separate entries.

When a user explicitly asks for a keyword update, make sure the keyword entry is backed by fresh code reading rather than only existing document text.

## 4. Responsibilities

- Primary responsibilities
- Public interfaces or exposed capabilities
- Upstream callers
- Downstream dependencies

## 5. Features

For each important feature:

- feature name
- existing document coverage, if any
- trigger or entrypoint
- main call chain in the form `entry -> layer -> core processing -> output or side effect`
- key files, classes, functions, handlers, or jobs involved
- what each major step in the call chain is responsible for
- important state, entities, or payloads
- input validation and decision points
- data transformation or state mutation through the flow
- storage writes, external API calls, events, cache operations, or other side effects
- error handling or boundary conditions
- concurrency, idempotency, retry, transaction, permission, or consistency considerations when relevant
- tests or code paths that provide evidence for the implementation details
- existing-document claim summary
- code-confirmed result
- doc-code mismatch or omitted implementation details

Do not keep feature descriptions at a surface level. For important features, explain the implementation path with enough detail that another engineer can follow the code without re-discovering the whole flow from scratch.

When existing documentation is in scope, do not merge old claims into the feature description silently. Explicitly separate:

- what the old document claimed
- what the code confirms
- what the old document missed or got wrong

## 6. Runtime and Configuration

- startup path
- environment variables
- config files or flags
- build or deployment notes that affect behavior

## 7. Existing Documentation Review

- mapped existing documents for this subsystem, if any
- which parts were reused, continued, or ignored
- where the old documentation matched the code
- where the old documentation conflicted with the code
- which important implementation details were missing from the old documentation

## 8. Related Detail Documents

- child detail documents that belong under this subsystem, if any
- what question or detail each child document covers
- where the parent document should link to each child document
- short parent-document summary text for each child document when applicable

## 9. Data and State

- core entities or models
- persistence or storage dependencies
- important state transitions

## 10. Tests and Evidence

- tests that validate key behavior
- file paths supporting the major claims

For important features, cite the most relevant implementation files, not just top-level directories.

## 11. Risks and Constraints

- high, medium, and low risks
- coupling points
- hidden assumptions
- technical debt

## 12. Open Questions

- `[needs-confirmation]` items
- recommended next files or runtime checks

## 13. Confidence-Annotated Findings

List the most important conclusions with labels:

- `[fact]`
- `[inference]`
- `[needs-confirmation]`
