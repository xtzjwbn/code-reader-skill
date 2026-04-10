# Existing Documents Inventory Template

Create this file during the pre-reading phase when existing-document detection is enabled and any related documents are found.

Suggested filename:

- `04-existing-docs-inventory.md`

## 1. Detection Scope

- output directory that was scanned
- other repository locations scanned for related documentation
- exclusions that affected document detection

## 2. Discovered Documents

For each discovered document:

- file path
- apparent topic coverage
- likely target mapping in the new documentation set
- apparent completeness: complete, partial, stale-suspected, or unclear
- why it appears relevant

## 3. Recommended Validation Mapping

For each relevant document:

- which subsystem, cross-system topic, or final document it maps to
- which code paths should be checked to validate it
- whether the document looks primarily architectural, operational, or implementation-focused

## 4. User Decision

- chosen handling mode for each relevant document set: `复用`, `续写`, or `重建`
- whether updates should happen in place or in a new file set
- any user instructions about keeping or ignoring specific legacy documents

## 5. Risks and Caveats

- documents that may be stale
- documents that conflict with each other
- documents that are too partial to trust without heavy code validation

## Rules

- similarity is decided by topic coverage and document responsibility first
- file names and directories are supporting signals only
- partial documentation must still be recorded
- code remains the source of truth even when a document looks complete
