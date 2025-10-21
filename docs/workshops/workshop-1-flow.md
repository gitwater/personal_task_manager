# Workshop Flow Documentation

This document outlines the development workflow process followed by the team for implementing the Personal Task Manager.

## Workshop Process Overview

The team followed a systematic approach using BMad workflows to move from product requirements to implementation-ready technical specifications.

## Step-by-Step Flow

### 1. Product Requirements Creation

**Workflow**: `/bmad:bmm:workflows:prd`

**Prompt Used**:
```
/bmad:bmm:workflows:prd

Generate a PRD for a simple task management application that:
- Allows users to create tasks with titles and optional notes
- Organizes tasks in three columns: To Do, Doing, and Done
- Enables task movement between status columns
- Supports editing and deleting tasks
- Shows completion summaries (tasks completed today and this week)
- Persists all data locally using SQLite
- Runs as a Next.js + React web application
- Focuses on individual accountability for informal, ad-hoc tasks
- Targets desktop browsers (1280px+ viewport)
- Maintains a clean, professional, business-appropriate aesthetic
```

**Review**: Team reviewed and aligned on the generated PRD

**Output**: `docs/PRD.md`

---

### 2. UX Specification

**Workflow**: `/bmad:bmm:workflows:ux-spec`

**Prompt Used**:
```
/bmad:bmm:workflows:ux-spec

@docs/PRD.md
```

**Review**: Examined the generated UX specification

**Decisions**:
- Aligned color scheme to match illustrateinc.com website
- Accepted the specification as-is

**Output**: `docs/ux-specification.md`

---

### 3. Solution Architecture

**Workflow**: `/bmad:bmm:workflows:solution-architecture`

**Prompt Used**:
```
/bmad:bmm:workflows:solution-architecture

@docs/PRD.md
@docs/ux-specification.md
```

**Review**: Quick review of the generated architecture document

**Outcome**: Accepted the architecture as proposed

**Output**: `docs/solution-architecture.md`

---

### 4. Epic Breakdown

**Workflow**: `/bmad:bmm:workflows:prd`

**Prompt Used**:
```
/bmad:bmm:workflows:prd

Generate epics.md from the solution architecture with the following structure for each epic:

1. Epic name, scope, and goals
2. Relevant user stories covered in PRD, and identify any additional stories needed beyond what's in the solution architecture
3. Dependencies
4. Key deliverables and acceptance criteria
5. Success metrics

IMPORTANT: The solution architecture may not have all necessary user stories defined to complete implementation of each epic. You should identify and list additional user stories you believe are needed beyond what's explicitly mentioned in the solution architecture.

DO NOT include:
- Granular implementation details (the tech-spec workflow will generate these)
- Detailed component specifications
- Technical implementation stories

Format each epic as a self-contained section that the tech-spec workflow can use as input to generate detailed technical specifications and implementation stories.

@docs/solution-architecture.md
```

**Review**: Validate epic breakdown covers all implementation phases

**Outcome**: Epic definitions provide clear scope boundaries for tech spec generation

**Output**: `docs/epics.md`

---

### 5. Technical Specification for Epic 1

**Workflow**: `/bmad:bmm:workflows:tech-spec`

**Prompt Used**:
```
/bmad:bmm:workflows:tech-spec

Generate tech spec for epic 1

@docs/epics.md
@docs/solution-architecture.md
```

**Review**: Quick review of the generated technical specification

**Output**: `docs/tech-spec-epic-1.md`

---

## Workflow Summary

```
PRD Creation (/bmad:bmm:workflows:prd)
    ↓
UX Specification (/bmad:bmm:workflows:ux-spec)
    ↓
Solution Architecture (/bmad:bmm:workflows:solution-architecture)
    ↓
Epic Breakdown (/bmad:bmm:workflows:prd)
    ↓
Tech Spec for Epic 1 (/bmad:bmm:workflows:tech-spec)
```

---

## Next Workshop

The next workshop will focus on iterative story creation and implementation:

### Story Creation Phase
For each story, run the following workflows to create the story and its context:
- `/bmad:bmm:workflows:create-story` - Generate the story file
- `/bmad:bmm:workflows:story-context` - Build story context and requirements
- `/bmad:bmm:workflows:testplan-arch` - Define test plan architecture

### Story Implementation Phase
For each story, run the following workflows to implement and validate:
- `/bmad:bmm:workflows:dev-story` - Develop the story implementation
- `/bmad:bmm:workflows:review-story` - Review and validate the implementation

### Iterative Process
Repeat the Story Creation → Story Implementation cycle until all Epic 1 stories are complete.

---

## Key Principles

1. **Systematic Progression**: Move through each phase methodically
2. **Review at Each Stage**: Quick reviews ensure alignment before proceeding
3. **Workflow-Driven**: Leverage BMad workflows for consistency and efficiency
4. **Documentation-First**: Each workflow produces artifacts that inform the next stage
