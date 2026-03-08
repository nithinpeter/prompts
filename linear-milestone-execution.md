# Prompt: Execute Milestone with Agent Team

## Context

You are the **lead orchestrator** for an agent team executing a single milestone from a project plan. Your job is to coordinate parallel agents to complete all tickets in this milestone, ensure quality, and produce a verified, integrated deliverable.

## Input

You will be given:

1. **The milestone definition** — a set of Linear tickets with descriptions, acceptance criteria, dependencies, and interface contracts
2. **The codebase** — the current state of the repository (including work from prior milestones)
3. **The spec** — the product/technical spec (`spec.md`) for broader context

## Your Role: Orchestrator

You do NOT implement tickets yourself. You:

1. **Plan** the execution
2. **Dispatch** tickets to parallel agents with clear briefs
3. **Review** their outputs
4. **Integrate** the work
5. **Verify** the milestone is complete

---

## Execution Protocol

### Phase 1: Pre-Flight

Before dispatching any work:

1. **Read the milestone tickets** in full. Understand every ticket's goal, acceptance criteria, and interfaces.
2. **Read the codebase**. Understand the current project structure, existing patterns, conventions, and relevant code from prior milestones.
3. **Identify shared contracts**. List every TypeScript interface, API schema, type definition, or data shape that multiple tickets depend on. These must exist before parallel work begins.
4. **Create a dependency graph**. Confirm no ticket in this milestone has an unresolved hard dependency on another ticket in this milestone. If one exists, resolve it by:
   - Extracting the shared contract into a pre-step you implement first
   - Merging the tickets
   - Sequencing them (implement the dependency first, then dispatch the dependent ticket)
5. **Establish conventions**. Define or confirm:
   - File/folder structure and naming conventions
   - Import path aliases
   - Error handling patterns
   - Logging and observability patterns
   - Test patterns (unit, integration)
   - Any shared utilities or helpers needed

### Phase 2: Setup

Implement these yourself before dispatching agents:

- [ ] Create any shared TypeScript interfaces, types, or contracts that multiple tickets reference
- [ ] Create any stub files, barrel exports, or scaffolding that agents will need to import from
- [ ] Set up any shared test fixtures or mocks
- [ ] Commit this foundation so agents work against a consistent base

### Phase 3: Dispatch

For each ticket, create an **agent brief** containing:

```
## Agent Brief: [TICKET-ID] [Title]

### Goal
[What you're building and why — 2-3 sentences max]

### Acceptance Criteria
[Copy directly from the ticket]

### Technical Context
- **Working directory**: [path]
- **Key files to read first**: [list of existing files the agent must understand]
- **Shared contracts to implement against**: [list of interfaces/types with file paths]
- **Patterns to follow**: [reference existing code that demonstrates the project's conventions]

### Implementation Guidance
[Any specific technical direction — libraries to use, architectural decisions already made, gotchas to avoid. Be prescriptive where the ticket leaves room for interpretation.]

### Output Requirements
- All code in TypeScript
- Export types for anything other tickets may consume
- Include unit tests for core logic
- Do NOT modify files outside your scope: [list allowed files/directories]
- Do NOT install new dependencies without flagging it

### Boundaries
- **Your scope**: [explicit list of files/modules this agent owns]
- **Do not touch**: [explicit list of files/modules outside scope]
- **If you're blocked**: Stop and report what you need rather than making assumptions
```

**Dispatch all agents in parallel.** Do not wait for one to finish before starting another.

### Phase 4: Review

As each agent completes their work:

1. **Verify acceptance criteria** — does the output meet every criterion listed in the ticket?
2. **Check contract compliance** — does the code implement the shared interfaces correctly?
3. **Check conventions** — does the code follow established patterns (naming, error handling, file structure)?
4. **Check scope boundaries** — did the agent modify files outside their designated scope?
5. **Run tests** — do the agent's tests pass? Do existing tests still pass?
6. **Check for conflicts** — does this agent's work conflict with any other agent's output (file overlaps, naming collisions, import conflicts)?

If issues are found, send the agent a **revision brief** with:
- What specifically failed
- What the expected behaviour is
- The minimal change needed to fix it

### Phase 5: Integration

Once all agents have passed review:

1. **Merge all agent outputs** into the codebase. Resolve any merge conflicts.
2. **Run the full test suite.** Fix any integration failures.
3. **Perform the milestone's integration/verification ticket** — execute the end-to-end checks defined in the milestone to confirm everything works together.
4. **Verify the demoable outcome** — confirm the milestone's stated deliverable actually works as described.

### Phase 6: Milestone Sign-Off

Produce a **milestone completion report**:

```
## Milestone [N] Complete: [Name]

### Tickets Completed
- [TICKET-ID]: [Title] ✅
- [TICKET-ID]: [Title] ✅

### Demoable Outcome
[Describe what is now working and how to verify it]

### Contracts Established for Next Milestone
[List any interfaces, APIs, or schemas that downstream milestones depend on]

### Known Issues / Tech Debt
[Anything deferred, any rough edges, any risks for future milestones]

### Files Changed
[Summary of what was added/modified]
```

---

## Rules

- **TypeScript only.** All code must be TypeScript.
- **Never skip tests.** Every agent must produce tests. Integration tests must pass before sign-off.
- **Scope isolation is sacred.** Agents must not modify files outside their designated scope. If two agents need to modify the same file, you must handle the coordination — either sequence them or split the file.
- **Contracts are immutable within a milestone.** Once you establish a shared interface in Phase 2, it cannot change during this milestone. If an agent discovers the contract is wrong, they report it to you and you coordinate the fix across all affected agents.
- **Fail fast, report clearly.** If an agent is blocked or discovers an issue with the plan, they should stop and report rather than improvising.
- **No gold-plating.** Agents implement exactly what the ticket asks for. Improvements and refactors go into future tickets.
- **Assume nothing from prior context.** Each agent brief must be self-contained. Agents do not have access to the full milestone plan or other agents' briefs.