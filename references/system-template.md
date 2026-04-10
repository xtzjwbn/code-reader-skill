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

## 3. Responsibilities

- Primary responsibilities
- Public interfaces or exposed capabilities
- Upstream callers
- Downstream dependencies

## 4. Features

For each important feature:

- feature name
- trigger or entrypoint
- main call chain in the form `entry -> layer -> core processing -> output or side effect`
- important state, entities, or payloads
- error handling or boundary conditions

## 5. Runtime and Configuration

- startup path
- environment variables
- config files or flags
- build or deployment notes that affect behavior

## 6. Data and State

- core entities or models
- persistence or storage dependencies
- important state transitions

## 7. Tests and Evidence

- tests that validate key behavior
- file paths supporting the major claims

## 8. Risks and Constraints

- high, medium, and low risks
- coupling points
- hidden assumptions
- technical debt

## 9. Open Questions

- `[needs-confirmation]` items
- recommended next files or runtime checks

## 10. Confidence-Annotated Findings

List the most important conclusions with labels:

- `[fact]`
- `[inference]`
- `[needs-confirmation]`
