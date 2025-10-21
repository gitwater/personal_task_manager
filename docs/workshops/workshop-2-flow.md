# Workshop 2 Flow Documentation

This document outlines the story creation and implementation workflow process followed by the team for Epic 1 of the Personal Task Manager.

## Workshop Process Overview

The team follows an iterative approach using BMad workflows to create and implement individual user stories. Each story goes through a Creation Phase (define and plan) followed by an Implementation Phase (develop and validate).

## Prerequisites

Before starting Workshop 2, ensure the following artifacts exist from Workshop 1:
- `docs/PRD.md`
- `docs/ux-specification.md`
- `docs/solution-architecture.md`
- `docs/tech-spec-epic-1-foundation.md`

---

## Story Iteration Workflow

For each user story in Epic 1, repeat the following Creation → Implementation cycle.

---

## Story Creation Phase

### Step 1: Create Story File

**Workflow**: `/bmad:bmm:workflows:create-story`

**Prompt Used**:
```
/bmad:bmm:workflows:create-story

Create the next story from Epic 1
```

**How It Works**:
- Workflow automatically discovers `tech-spec-epic-1-foundation.md` using pattern matching
- Automatically loads all architecture documents from `docs/` folder
- Reads `epics.md` to verify the next story is planned (will HALT if not enumerated)
- Pulls requirements from PRD.md as fallback

**Review**: Examine the generated story file for clarity and completeness

**Output**: `docs/stories/story-<ID>.md`

---

### Step 2: Build Story Context

**Workflow**: `/bmad:bmm:workflows:story-context`

**Prompt Used**:
```
/bmad:bmm:workflows:story-context

Build context for the story

@docs/stories/story-<ID>.md

```

**Review**: Verify that story context includes all necessary requirements, dependencies, and technical considerations

**Output**: Story context document (embedded in story file or separate context file)

---

### Step 3: Plan Test Coverage

**Workflow**: `/bmad:bmm:workflows:testarch-plan`

**Prompt Used**:
```
/bmad:bmm:workflows:testarch-plan

Plan risk mitigation and test coverage

@docs/stories/story-<ID>.md
```

**Review**: Ensure test plan covers unit tests, integration tests, and E2E scenarios appropriate to the story

**Output**: Test coverage plan with risk mitigation strategies

---

## Story Implementation Phase

### Step 4: Develop Story

**Workflow**: `/bmad:bmm:workflows:dev-story`

**Prompt Used**:
```
/bmad:bmm:workflows:dev-story

Implement the story

@docs/stories/story-<ID>.md
```

**Actions**:
- Agent implements code changes
- Agent writes unit and integration tests
- Agent ensures code passes linting and type checking
- Agent commits changes

**Output**:
- Implementation code
- Test files
- Git commit(s)

---

### Step 5: Review Story

**Workflow**: `/bmad:bmm:workflows:review-story`

**Prompt Used**:
```
/bmad:bmm:workflows:review-story

Review the implementation

@docs/stories/story-<ID>.md
```

**Review Checklist**:
- All acceptance criteria met
- Tests passing (unit, integration, E2E)
- Code quality meets standards
- No regressions introduced
- Documentation updated

**Decisions**:
- Accept implementation and move to next story
- Request revisions if issues found

**Output**: Story marked as complete

---

## Iteration Summary

```
For each story in Epic 1:

Story Creation Phase:
  create-story (/bmad:bmm:workflows:create-story)
      ↓
  story-context (/bmad:bmm:workflows:story-context)
      ↓
  testarch-plan (/bmad:bmm:workflows:testarch-plan)
      ↓

Story Implementation Phase:
  dev-story (/bmad:bmm:workflows:dev-story)
      ↓
  review-story (/bmad:bmm:workflows:review-story)
      ↓

[Story Complete - Repeat for Next Story]
```

---

## Epic 1 Story Sequence

The stories for Epic 1 (Foundation & Core Infrastructure) should be created and implemented in the following order:

1. **Story 1**: Project initialization and configuration
2. **Story 2**: Database schema and migrations
3. **Story 3**: Basic task CRUD operations
4. **Story 4**: Task status workflow (To Do → Doing → Done)
5. **Story 5**: Task board UI foundation
6. **Story 6**: Task completion summary

*(Note: Exact story breakdown may vary based on tech spec and architect decisions)*

---

## Key Principles

1. **One Story at a Time**: Complete the full Creation → Implementation cycle for one story before starting the next
2. **Context-Driven**: Each story builds on the context established in earlier stories
3. **Test-First Mindset**: Define test plan architecture before implementing code
4. **Review Before Proceeding**: Ensure story passes review before moving to the next story
5. **Iterative Refinement**: Use learnings from each story to improve subsequent iterations

---

## Epic Completion Criteria

Epic 1 is considered complete when:
- All stories have been created, implemented, and reviewed
- All tests are passing (unit, integration, E2E)
- Application meets MVP requirements from PRD
- Code quality gates pass (TypeScript strict mode, ESLint)
- No critical bugs or regressions

---

## Next Steps After Workshop 2

Once Epic 1 is complete:
- Return to Workshop 1 pattern for Epic 2 (create tech-spec for Epic 2)
- Apply same Story Creation → Implementation workflow for Epic 2 stories
- Continue until all epics required for MVP are complete
