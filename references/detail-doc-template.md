# Detail Child Document Template

Use this template when the user asks for a dedicated child document about a detail, feature, or implementation topic inside an existing system.

The child document should live under a system-specific child folder inside the documentation directory.

## 1. Parent Context

- parent system document
- subsystem name
- reason this topic deserves a child document

## 2. Topic Definition

- the exact detail, feature, or implementation question this document answers
- why it matters
- related keywords

## 3. Code Entry and Scope

- relevant entrypoints
- relevant files, classes, functions, handlers, jobs, config keys, or schema names
- scope boundaries for this child document

## 4. Detailed Logic

- step-by-step implementation flow
- important branching or decision points
- data transformations and state changes
- side effects, remote calls, events, writes, cache behavior, or scheduled work

## 5. Exceptions and Constraints

- error paths
- edge cases
- permission, consistency, retry, transaction, or concurrency considerations when relevant

## 6. Relationship to Parent Document

- what the parent document already said
- what this child document expands
- short summary text suitable for linking from the parent document

## 7. Evidence

- key file paths supporting the explanation
- tests or runtime checks that validate the behavior

## 8. Confidence-Annotated Findings

- `[fact]`
- `[inference]`
- `[needs-confirmation]`
