# BMAd Developer Flow

## Planning & Implementation

### BMAd Method v6a

We use the BMAd Method v6a agents to accelerate and standardize our development process while maintaining quality. This is a temporary system that we will use until we are eating our own dog food with the Compounds.dev platform.

**BMAd Agent Roles:**

| Agent | Command | Responsibility | Primary Use |
|-------|---------|---------------|-------------|
| PM | `/bmad:bmm:agents:pm` | Product Manager | PRD creation, project planning, requirements analysis, high-level task breakdown |
| Architect | `/bmad:bmm:agents:architect` | Solution Architecture | Architecture design, technology evaluation, ADRs, spec file creation |
| UX Expert | `/bmad:bmm:agents:ux-expert` | User Experience Design | UX specifications, user interface designs, user journey mapping |

**BMAd Workflows:**

### Scoping

| Workflow | Purpose | When Used |
|----------|---------|-----------|
| `/bmad:bmm:workflows:prd` | Create comprehensive Product Requirements Document | Large projects - initial scoping phase |

### Architecting

| Workflow | Purpose | When Used |
|----------|---------|-----------|
| `/bmad:bmm:workflows:solution-architecture` | Design solution architecture with technology choices and ADRs | Large projects - after PRD approval |
| `/bmad:bmm:workflows:ux-spec` | Create UX specifications and user interface designs | Large projects - during architecture phase |

### Planning

| Workflow | Purpose | When Used |
|----------|---------|-----------|
| `/bmad:bmm:workflows:create-story` | Generate detailed task from spec/PRD and architecture | Medium & Large projects - start of each task |
| `/bmad:bmm:workflows:story-context` | Assemble codebase context and ground task in current reality | Medium & Large projects - after task creation |
| `/bmad:bmm:workflows:testarch-plan` | Plan comprehensive test strategy based on risk assessment | Medium & Large projects - before implementation |

### Implementing

| Workflow | Purpose | When Used |
|----------|---------|-----------|
| `/bmad:bmm:workflows:testarch-atdd` | Generate failing acceptance tests (ATDD approach) | Medium & Large projects - optional but recommended |
| `/bmad:bmm:workflows:dev-story` | Execute task implementation with tests | Medium & Large projects - implementation phase |
| `/bmad:bmm:workflows:review-story` | Senior developer quality validation and approval | Medium & Large projects - final approval gate |

## Three Implementation Flows

We use three distinct implementation approaches based on project scale. Each approach balances documentation overhead with project complexity:

| Scale | Approach | Description | Best For |
|-------|----------|-------------|----------|
| Small | Prompt Engineering | Direct implementation using prompt engineering to guide the dev agent | Bug fixes, minor changes, single-file modifications |
| Medium | Spec Engineering | Create lightweight spec file, then follow 6-Step Task Development Flow | Single task projects requiring architecture alignment |
| Large | Context Engineering | Full documentation with context files, then 6-Step Task Development Flow per task | Multi-task projects requiring comprehensive planning |

## Small Projects: Prompt Engineering

**When to use:** Bug fixes, minor changes, or simple modifications

**Documentation:** None required

**Process:**

1. **Craft the prompt**
   - Explain what needs to change and why
   - Point the dev agent to relevant architecture documentation
   - Reference specific source code files that need modification
   - Provide context about the system behavior

2. **Implement directly**
   - Use the crafted prompt to guide implementation
   - Make the change directly
   - Review the change before committing

**Example prompt structure:**

```
We need to fix [bug/add feature X] in [component Y].

Architecture context:
- See: /docs/architecture/[relevant-doc].md
- Pattern: [describe the pattern used]

Source files:
- /src/[file1.ts] - [what needs to change]
- /src/[file2.ts] - [related file]

Change needed: [specific description]
```

**Approval:** Standard code review before merge

## Medium Projects: Spec Engineering

**When to use:** Single task (story) worth of work requiring architecture and implementation alignment

**Documentation:** Spec file (~500 lines combining PRD + Solution Architecture + implementation guidance)

**Process:**

### 1. Create Spec File
- **Who:** PM/Architect agent
- **What:** Single document (~500 lines) containing:
  - Problem statement and user value (mini-PRD)
  - Solution architecture approach
  - Technical implementation guidance
  - Acceptance criteria
  - Testing approach

### 2. Approval Gate
- **Who:** Product Owner + Technical Lead
- **What:** Review spec file for product alignment and technical feasibility
- **Outcome:** Both must approve before proceeding

### 3. Execute Task
- Follow the **[6-Step Task Development Flow](#core-workflow-6-step-task-development-flow)** (see below)
- Use the approved spec file as input to Step 1

### 4. Mark Complete
- Update task to DONE
- Document completion date and points

## Large Projects: Context Engineering

**When to use:** Multiple tasks (stories) requiring comprehensive planning and architectural decisions

**Documentation:**
- PRD (Product Requirements Document)
- Solution Architecture
- High-level Project with tasks file
- High-level Task files (one per task)

**Process:**

### Planning Phase

#### Step 1: Create PRD
- **Who:** PM agent using `/bmad:bmm:agents:pm` → `*prd`
- **What:** Comprehensive product requirements document

#### Step 2: PRD Approval Gate
- **Who:** Product Owner (Product Vision) + Technical Lead (Technical Feasibility)
- **What:** Review PRD for product alignment and technical soundness
- **Outcome:** Both must approve before proceeding to solution architecture

#### Step 3: Create Solution Architecture
- **Who:** Architect agent
- **What:** Solution architecture document including:
  - Architecture design and patterns
  - Technology choices and rationale (ADRs)
  - Risk assessment
  - System integration points

#### Step 4: Architecture Approval Gate
- **Who:** Technical Lead (with team input)
- **What:** Review architecture approach
- **Outcome:** Approve architecture before task breakdown

#### Step 5: Create High-Level Project File
- **Who:** PM agent
- **What:** Document listing all tasks in the project with:
  - Task ordering and dependencies
  - Estimated effort per task
  - Milestone groupings

#### Step 6: Create High-Level Task Files
- **Who:** PM agent
- **What:** One file per task containing:
  - Task objectives
  - High-level acceptance criteria
  - Prerequisites and dependencies
  - Success metrics

### Implementation Phase (Iterative, Per Task)

For each task in the project:

1. Follow the **[6-Step Task Development Flow](#core-workflow-6-step-task-development-flow)** (see below)
2. Use the high-level task file as input to Step 1
3. Include PRD and Solution Architecture as additional context in Step 2
4. Mark task complete after Step 6 approval

---

## Core Workflow: 6-Step Task Development Flow

This is the standard workflow used for both Medium and Large projects. Once you have a task defined (either from a spec file or from a high-level task file), follow these steps:

### Step 1: Create Task (`/bmad:bmm:workflows:create-story`)
- **Input:** Spec file (Medium) or High-level task file (Large)
- **Output:** Detailed task markdown with objectives, acceptance criteria, sub-tasks

### Step 2: Assemble Task Context (`/bmad:bmm:workflows:story-context`)
- **Input:** Task file, current codebase, documentation, PRD (if applicable), Solution Architecture (if applicable)
- **Output:** `.story-{n}-context.xml` with:
  - Relevant code files and patterns
  - Existing implementations to reference
  - Technical constraints
  - Integration points
  - Required dependencies
- **Critical:** This step ensures AI-generated code aligns with existing patterns and architecture

### Step 3: Plan Test Coverage (`/bmad:bmm:workflows:testarch-plan`)
- **Input:** Task file, context file, current test coverage, codebase entropy
- **Output:** Test strategy with:
  - Test level recommendations (unit, integration, E2E)
  - Risk assessment
  - Coverage targets
  - Test data requirements

### Step 4: Generate Acceptance Tests (`/bmad:bmm:workflows:testarch-atdd`) [Optional but recommended]
- **Input:** Task acceptance criteria, test plan, context
- **Output:** Failing acceptance test suite (ATDD approach)

### Step 5: Implement Task (`/bmad:bmm:workflows:dev-story`)
- **Input:** Task file, context, test plan, acceptance tests
- **Output:** Implementation code, unit tests, integration tests, documentation, all tests passing

### Step 6: Review Task (`/bmad:bmm:workflows:review-story`)
- **Input:** Implementation, test suite, task acceptance criteria, PRD/architecture (if applicable)
- **Output:** Review feedback covering quality, architecture alignment, test coverage, security, performance
- **Approval Gate:** Task cannot be marked complete without review approval

**Task States:**
```
BACKLOG → TODO → IN PROGRESS → IN REVIEW → DONE
```
