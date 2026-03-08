# Prompt: Generate Linear Tickets from Spec

## Context

You are a senior technical project planner. Your job is to read a product/technical spec and produce a complete set of Linear tickets organized into **milestones** (also called **waves** or **execution cycles**).

Create all milestones, labels, and issues directly in Linear using the Linear MCP tools (`save_milestone`, `save_issue`, `create_issue_label`, `list_issue_labels`).

## Linear Project

| Field | Value |
|---|---|
| **Project** | https://linear.app/westringia/project/accountant-f4c8fd983d52/overview
| **Team** | Westringia (`WES`) |
| **Team ID** | `a1985e87-2bec-40ef-9059-62bf6f270e9c` |

All issues must be created with **team**: `Westringia`, **project**: `Accountant`, and **state**: `Backlog`. Assign each issue to the appropriate milestone by name. After all issues are created, wire up dependencies using `save_issue` with `blocks` / `blockedBy` fields.

## Input

Read the file `spec.md` in this repository. This is the source of truth for all features and requirements.

## Output Requirements

### Ticket Structure

For each ticket, produce:

- **Title**: Clear, action-oriented (e.g. "Implement property search API endpoint")
- **Description**: Enough context that a developer (or AI agent) can pick this up and work on it independently, without needing to ask clarifying questions. Include:
  - **Goal**: What this ticket achieves and why
  - **Acceptance Criteria**: Specific, testable conditions for "done"
  - **Technical Approach** (where applicable): Key implementation notes, data models, API contracts, libraries, or patterns to use
  - **Dependencies**: Explicit list of ticket IDs this blocks or is blocked by
  - **Interfaces / Contracts**: If other tickets depend on this one, define the API surface, types, or data shapes that downstream tickets can code against _before_ this ticket is complete
- **Labels**: e.g. `frontend`, `backend`, `infra`, `design`, `research`
- **Estimate**: T-shirt size (S / M / L / XL)
- **Priority Labels**: critical_path, important, nice_to_have

### Milestone / Wave Organization

Group tickets into sequential **milestones** (waves). Each milestone represents one execution cycle that will be worked on by a parallel team of agents (developers, designers, researchers).

**Rules for milestones:**

1. **Sequential execution**: Milestones are executed one after another. Milestone N must be fully complete before Milestone N+1 begins.
2. **Internal parallelism**: Within a single milestone, ALL tickets should be executable in parallel by independent agents. If ticket A within a milestone depends on ticket B in the same milestone, they must either:
   - Be merged into one ticket, OR
   - Define a shared interface/contract upfront so both can be built simultaneously against that contract, OR
   - Be moved so the dependency is in an earlier milestone
3. **Self-contained completeness**: Each milestone should produce a tangible, testable increment of the product. After completing a milestone, the team should be able to demo or verify something works end-to-end.
4. **Integration tickets**: Each milestone (after the first) should include an **integration/verification ticket** that validates the work from that wave connects correctly with prior milestones.
5. **Contract-first design**: Where a milestone's tickets need to interoperate (e.g. frontend + backend), define TypeScript interfaces, API schemas, or data contracts as a ticket in the current or preceding milestone so parallel agents can code against them.

### Milestone Progression Pattern

Follow this general progression (adapt to the spec):

- **Milestone 0 — Foundation**: Project scaffolding, shared types/interfaces, CI/CD, dev environment, design system tokens. Everything downstream depends on.
- **Milestone 1 — Core Data & Infrastructure**: Database schemas, core API endpoints, auth, key backend services.
- **Milestone 2 — Core Features (Backend + Frontend shells)**: Primary feature implementations. Backend logic + frontend components built in parallel against shared contracts.
- **Milestone 3 — Feature Completion & Integration**: Secondary features, edge cases, cross-feature integration, error handling.
- **Milestone 4 — Polish & Production Readiness**: Performance optimization, monitoring, documentation, deployment pipeline, UAT.

### Tech Stack

- TypeScript throughout (frontend and backend)
- Specify framework/library choices in relevant tickets based on what the spec requires

## Quality Checks

Before finalizing, verify:

- [ ] Every ticket can be picked up cold by an agent with no additional context beyond the ticket description and the codebase
- [ ] No two tickets within the same milestone have a hard dependency on each other (unless a shared contract is defined)
- [ ] Every milestone produces a verifiable, demoable outcome
- [ ] Acceptance criteria are specific and testable (not vague like "works correctly")
- [ ] Interface/contract tickets exist wherever parallel tickets need to interoperate
- [ ] No orphan tickets — every ticket traces back to a spec requirement
- [ ] Estimates reflect actual complexity, not just code volume
