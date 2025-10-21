# Claude Code Skills - Phase 1: Minimum Viable Skill Set

This directory contains Claude Code skills that accelerate and standardize the BMAd development process. These skills ensure consistency, completeness, and quality across all phases of software development.

## What Are Skills?

Skills are specialized capabilities that Claude can invoke to perform specific, complex tasks. Unlike agents (which are autonomous), skills are tools that guide Claude's analysis and output generation in a structured, repeatable way.

## Phase 1: Critical Path Skills

The five skills in this directory represent the **Minimum Viable Skill Set** - the highest-impact skills that reduce risk and ensure quality in BMAd projects.

---

## Skill 1: prd-to-task-decomposition

**Purpose:** Systematically decompose a PRD into a complete task list ensuring all requirements are covered.

**Use When:**
- BMAd Planning Phase, Step 5 (Create High-Level Project File)
- Starting a Large Project and need task breakdown
- Validating existing task decomposition for completeness

**Input:**
- Product Requirements Document (PRD) markdown file

**Output:**
- Numbered task list with FR/NFR mappings
- Coverage validation showing all requirements addressed
- Cross-cutting requirements identification
- Rationale for each task grouping

**Example Usage:**
```
Please use the prd-to-task-decomposition skill to analyze docs/prds/personal_task_manager.md and generate a complete task breakdown.
```

**Key Benefits:**
- Ensures NO requirements are orphaned (missed)
- Creates logical, cohesive task boundaries
- Identifies cross-cutting concerns (e.g., testing NFRs that apply to all tasks)
- Provides clear requirement → task traceability

---

## Skill 2: requirements-traceability-matrix

**Purpose:** Generate and maintain a traceability matrix tracking requirements → tasks → tests to ensure complete coverage.

**Use When:**
- After Planning Phase Step 6 (validate task decomposition)
- After each task completion (update matrix with progress)
- Before final release (validate 100% coverage)
- When requirements change (identify impact)

**Input:**
- PRD markdown file
- Task files (high-level or detailed)
- Test files (if implementation phase)

**Output:**
- Comprehensive traceability matrix (requirements → tasks → tests)
- Coverage metrics and gap analysis
- Orphaned requirements identification
- Gold-plating detection (features not tied to requirements)
- Untested requirements flagging

**Example Usage:**
```
Please use the requirements-traceability-matrix skill to analyze:
- PRD: docs/prds/personal_task_manager.md
- Tasks: docs/tasks/*.md
- Tests: src/**/*.test.ts

Generate a traceability report showing coverage status.
```

**Key Benefits:**
- Prevents orphaned requirements (defined but never implemented)
- Detects gold-plating (implemented but not required)
- Ensures test coverage for every requirement
- Provides clear progress tracking across project lifecycle

---

## Skill 3: risk-based-test-planner

**Purpose:** Analyze task risk and complexity to recommend appropriate test strategy with justified coverage targets.

**Use When:**
- BMAd Implementation Phase, Step 3 (Plan Test Coverage) for each task
- Deciding if a task needs more or less testing
- Validating overall testing strategy during architecture review

**Input:**
- Task file (detailed story with acceptance criteria)
- Related functional and non-functional requirements
- Test plan requirements from PRD

**Output:**
- Comprehensive test strategy document
- Risk assessment (failure probability × impact severity)
- Test level recommendations (unit, integration, E2E) with coverage targets
- Edge cases and boundary conditions identification
- Test data strategy (factories, fixtures, mocks)
- Test effort estimation

**Example Usage:**
```
Please use the risk-based-test-planner skill to create a test strategy for:
- Task: docs/tasks/task-002-database-layer.md
- PRD: docs/prds/personal_task_manager.md

Generate a comprehensive test plan.
```

**Key Benefits:**
- Consistent, justified coverage targets (not arbitrary numbers)
- Risk-based approach ensures critical paths get more testing
- Identifies edge cases that might be missed
- Recommends appropriate test data strategy
- Balances test effort with risk mitigation value

---

## Skill 4: comprehensive-story-reviewer

**Purpose:** Comprehensive quality gate review for completed tasks across 7 dimensions: acceptance criteria, code quality, architecture, tests, security, performance, and integration.

**Use When:**
- BMAd Implementation Phase, Step 6 (Review Task) for every task
- Before marking any task as DONE
- Before merging code to main branch
- As final quality gate for task completion

**Input:**
- Task file (with acceptance criteria)
- Implementation code (all modified/created files)
- Test files and coverage report
- Architecture Decision Records (ADRs)
- PRD requirements

**Output:**
- Comprehensive review report with pass/fail across 7 dimensions
- Final verdict: APPROVED | APPROVED WITH RECOMMENDATIONS | CHANGES REQUIRED
- Blocking issues (must fix)
- High-priority recommendations (should fix)
- Low-priority suggestions (nice to have)

**Example Usage:**
```
Please use the comprehensive-story-reviewer skill to review:
- Task: docs/tasks/task-002-database-layer.md
- Implementation: src/lib/database/* and src/lib/models/*
- Tests: src/lib/database/*.test.ts
- ADRs: docs/architecture/adrs/*

Generate a comprehensive review report.
```

**Key Benefits:**
- Consistent quality standards across all 12 tasks
- Multi-dimensional review (not just code quality)
- Validates architecture alignment (ADR compliance)
- Ensures test coverage quality (not just quantity)
- Catches security and performance issues early
- Clear pass/fail criteria with actionable feedback

---

## Skill 5: task-type-detector-and-context-assembler

**Purpose:** Detect task type and assemble appropriate technical context including architecture patterns, ADRs, and implementation guidance.

**Use When:**
- BMAd Implementation Phase, Step 2 (Assemble Task Context) for each task
- Before starting implementation on any task
- When developers need to understand the technical approach

**Input:**
- Task file (detailed story with objectives and acceptance criteria)
- Architecture Decision Records (ADRs)
- PRD (for constraints and requirements)
- Existing codebase (for brownfield projects)

**Output:**
- Comprehensive technical context document with:
  - Task type classification (data layer, UI, API, business logic, infrastructure, integration)
  - Relevant ADRs with implementation guidance
  - Technology-specific patterns and best practices
  - Technical constraints and their implications
  - Dependencies and integration points
  - Anti-patterns to avoid
  - Recommended implementation approach

**Example Usage:**
```
Use the task-type-detector-and-context-assembler skill to assemble context for Task 2: Database Layer.
```

**Key Benefits:**
- Ensures implementation aligns with architecture from the start
- Provides type-appropriate context (data tasks get schema patterns, UI tasks get component patterns)
- References relevant ADRs automatically
- Identifies technical constraints and integration points
- Prevents architecture drift by surfacing anti-patterns

---

## Skill Workflow: How They Work Together

### Planning Phase (Before Implementation)

**Step 1:** Use `prd-to-task-decomposition`
- Input: PRD
- Output: Task list with FR/NFR mappings
- **Validates:** All requirements are assigned to tasks

**Step 2:** Use `requirements-traceability-matrix`
- Input: PRD + Task list
- Output: Coverage matrix
- **Validates:** No orphaned requirements, no gaps

### Implementation Phase (Per Task)

**Step 1:** Create detailed task (manual or agent)
- Expand high-level task into detailed story

**Step 2:** Use `task-type-detector-and-context-assembler`
- Input: Task file + ADRs + PRD
- Output: Technical context document
- **Ensures:** Right patterns and architecture alignment

**Step 3:** Use `risk-based-test-planner`
- Input: Task file + Context + PRD
- Output: Test strategy
- **Guides:** Test implementation with justified targets

**Step 4:** Generate acceptance tests (optional - ATDD approach)
- Write failing tests for acceptance criteria

**Step 5:** Implement task and tests
- Developer/AI follows context and test plan

**Step 6:** Use `comprehensive-story-reviewer`
- Input: Task + Implementation + Tests + ADRs + Test Plan
- Output: Review report with approval decision
- **Validates:** Quality across 7 dimensions

**Step 7:** Update `requirements-traceability-matrix`
- Input: Updated task status + test files
- Output: Updated matrix showing progress
- **Tracks:** Which requirements are now implemented and tested

---

## Quick Reference: When to Use Each Skill

| Phase | Step | Skill | Purpose |
|-------|------|-------|---------|
| Planning | 5 | prd-to-task-decomposition | Break PRD into tasks |
| Planning | 6 | requirements-traceability-matrix | Validate coverage |
| Implementation | 2 (per task) | task-type-detector-and-context-assembler | Assemble technical context |
| Implementation | 3 (per task) | risk-based-test-planner | Plan testing strategy |
| Implementation | 6 (per task) | comprehensive-story-reviewer | Quality gate review |
| Any time | - | requirements-traceability-matrix | Track progress |

---

## Project Scale Considerations

These skills are optimized for **Large Projects** (multiple tasks, full BMAd flow) as defined in `docs/bmad-process.md`.

### For Large Projects (10+ FRs, multiple tasks)
**Use all 5 skills:**
- ✅ `prd-to-task-decomposition` - Break into 8-15 tasks
- ✅ `requirements-traceability-matrix` - Track coverage across all tasks
- ✅ `task-type-detector-and-context-assembler` - Context for each task
- ✅ `risk-based-test-planner` - Test strategy for each task
- ✅ `comprehensive-story-reviewer` - Review each task before DONE

**Workflow:** Full BMAd Large Project flow (Planning Phase → Implementation Phase with 6 steps per task)

### For Medium Projects (single task/spec)
**Use skills selectively:**
- ✅ `prd-to-task-decomposition` - Validate it's truly single-task (should yield 1 task, not many)
- ⚠️ `requirements-traceability-matrix` - Optional (only one task to track)
- ✅ `task-type-detector-and-context-assembler` - Context for the task
- ✅ `risk-based-test-planner` - Test strategy for the task
- ✅ `comprehensive-story-reviewer` - Review before completion

**Workflow:** BMAd Spec Engineering approach (create lightweight spec file, then execute)

### For Small Projects (bug fixes, minor changes)
**Skip skills, use prompt engineering:**
- ❌ Skills are overkill for small changes
- ✅ Use direct prompting with architecture context
- ✅ Follow BMAd Small Projects: Prompt Engineering approach

**Example:** "Fix bug in validation logic - see architecture doc at docs/architecture/validation.md"

### How to Decide Project Scale

Run `prd-to-task-decomposition` on your PRD:
- **Result: 1-2 cohesive tasks** → Medium Project (use spec engineering)
- **Result: 3-15 tasks** → Large Project (use full skill set)
- **Result: Bug fix or single-file change** → Small Project (use prompt engineering)

---

## Installation and Usage

### Skills are Already Available

These skills are stored in `.claude/skills/` and are automatically available to Claude Code.

### How to Invoke a Skill

Simply ask Claude to use the skill by name:

```
Please use the prd-to-task-decomposition skill to analyze my PRD.
```

Or be more specific:

```
Use the risk-based-test-planner skill to create a test strategy for the database layer task.
```

### Skills vs. Agents

**Skills:**
- Invoked explicitly by Claude (or by you asking Claude to use them)
- Provide structured analysis and output generation
- Don't execute code or make changes autonomously
- Act as "expert advisors" for specific analyses

**Agents:**
- Run autonomously to complete multi-step tasks
- Execute tools (read files, run commands, etc.)
- Return results when done
- Act as "automated workers"

---

## Examples: Using Skills on Personal TaskBoard PRD

### Example 1: Planning Phase

**You:** "I have a PRD at docs/prds/personal_task_manager.md. Let's use the BMAd process to plan this project."

**Claude uses:**
1. `prd-to-task-decomposition` → Generates 12 tasks covering FR1-FR10 and NFR1-NFR11
2. `requirements-traceability-matrix` → Validates all requirements are assigned

**Output:** Complete task breakdown with coverage validation

---

### Example 2: Starting a Task

**You:** "I'm ready to implement Task 2: Database Layer. Help me plan the testing."

**Claude uses:**
1. `risk-based-test-planner` → Analyzes database layer task

**Output:** Test strategy showing:
- Risk: High (data integrity + complexity)
- Unit tests: 85-90% coverage
- Integration tests: 100% of CRUD operations
- Edge cases: Empty values, concurrent updates, DB failures

---

### Example 3: Reviewing a Completed Task

**You:** "I've completed Task 2. Review it before I mark it DONE."

**Claude uses:**
1. `comprehensive-story-reviewer` → Reviews code, tests, architecture, security, performance

**Output:** Review report with verdict:
- ✅ Acceptance criteria: PASS (all ACs met)
- ✅ Code quality: PASS
- ✅ Architecture: PASS (Drizzle ADR followed)
- ⚠️ Test coverage: PASS WITH GAPS (missing edge case for concurrent updates)
- ✅ Security: PASS
- ⚠️ Performance: PASS (meets NFR2, but N+1 query detected)
- ✅ Integration: PASS

**Verdict:** APPROVED WITH RECOMMENDATIONS
- Fix N+1 query in next iteration
- Add edge case test for concurrent updates

---

### Example 4: Checking Overall Progress

**You:** "Show me our progress on the Personal TaskBoard project."

**Claude uses:**
1. `requirements-traceability-matrix` → Scans all tasks and tests

**Output:** Matrix showing:
- FR1-FR3: ✅ Implemented and tested (Tasks 2, 3, 6, 7 DONE)
- FR4-FR10: 📋 Not started (Tasks 8-12 in BACKLOG)
- NFR3, NFR4: ✅ Validated in all completed tasks
- NFR5: ⚠️ Coverage at 68% (target: 70%) - needs improvement
- Overall: 30% complete (3/10 FRs implemented)

---

## Skill Quality Standards

Each skill is designed to:
- **Be deterministic:** Same input → same output structure
- **Be thorough:** Check ALL relevant aspects, not just obvious ones
- **Be actionable:** Provide specific, file-level recommendations
- **Be justified:** Explain WHY something is recommended
- **Be consistent:** Apply same standards every time

---

## Future Skill Additions (Tier 2 & 3)

After Phase 1 skills are validated, consider adding:

**Tier 2 (High-Value Accelerators):**
- `high-level-task-file-generator` - Generate task markdown files
- `detailed-story-generator` - Expand high-level tasks into detailed stories
- `atdd-test-generator` - Generate failing acceptance tests from ACs
- `adr-compliance-checker` - Real-time ADR violation detection

**Tier 3 (Quality-of-Life):**
- `prd-quality-validator` - Validate PRD completeness
- `task-dependency-analyzer` - Determine task ordering
- `complexity-estimator` - Estimate task effort
- `edge-case-identifier` - Extract edge cases from requirements

---

## Contribution Guidelines

When adding new skills:

1. **Follow the template:**
   - Frontmatter with name and description
   - Core expertise section
   - Clear methodology
   - Structured output format
   - When to use section

2. **Test thoroughly:**
   - Validate with real PRDs and tasks
   - Ensure output is actionable
   - Check for edge cases

3. **Document clearly:**
   - Add to this README
   - Provide usage examples
   - Explain integration with BMAd process

---

## Support and Feedback

If a skill produces unexpected results or could be improved:

1. Review the skill prompt for clarity
2. Test with different inputs to identify patterns
3. Refine the skill prompt based on learnings
4. Update this README with new insights

Skills improve through iteration - treat them as living documents that evolve with your process.

---

**Version:** 1.0
**Last Updated:** 2025-10-21
**Status:** Phase 1 Complete - Ready for Production Use
