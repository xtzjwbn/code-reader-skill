# Final Repository Document Template

This template is for the final merged document only. It does not replace the required pre-reading artifacts that must be generated earlier in the workflow.

After this final merged document is complete, also produce a separate `99-overall-summary.md` file that gives a top-down overview of the entire documentation set.

Use this structure when the total-control agent merges subsystem writeups into the final knowledge document.

## 1. Repository Overview

- purpose of the repository
- primary runtime model
- major deployables or subsystems

## 2. Technology Stack

- languages
- frameworks
- package or dependency systems
- infrastructure or platform dependencies

## 3. System Map

- top-level subsystem list
- ownership boundaries
- notable shared libraries or platform layers

## 4. Architecture

- major layers
- important runtime boundaries
- key request, event, or job paths

## 5. Subsystem Deep Dives

One subsection per subsystem using a normalized summary of the subsystem writeups.

## 6. Core Features and End-to-End Flows

Focus on the highest-value flows across subsystem boundaries.

## 7. Configuration Model

- environment variables
- config files
- feature flags
- secret or deployment assumptions

## 8. Data Model and State

- shared entities
- storage systems
- important state transitions

## 9. Build, Test, Release, and Deployment

- local run path
- build path
- test strategy
- CI and release flow

## 10. External Integrations

- third-party APIs
- infrastructure dependencies
- internal services outside the repository

## 11. Risks, Technical Debt, and Constraints

- architectural risks
- fragile areas
- hidden coupling
- operational concerns

## 12. Open Questions

Keep unresolved items separate from confirmed findings.

## 13. Suggested Onboarding Path

Recommend the order a new engineer should read the code and docs.
