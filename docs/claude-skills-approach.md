# Claude Skills: Discovery and Design Methodology

**Project:** Personal TaskBoard
**Author:** Kyle Lindsay
**Date:** 2025-10-21
**Purpose:** Document the methodology for discovering, designing, and implementing Claude Code skills

---

## Table of Contents

1. [What Are Claude Skills?](#what-are-claude-skills)
2. [Skills vs. Agents: Key Differences](#skills-vs-agents-key-differences)
3. [The Discovery Process](#the-discovery-process)
4. [Simulation-Driven Discovery](#simulation-driven-discovery)
5. [Pain Point Analysis](#pain-point-analysis)
6. [Skill Design Principles](#skill-design-principles)
7. [Prioritization Framework](#prioritization-framework)
8. [Our Skills: A Case Study](#our-skills-a-case-study)
9. [Reusable Methodology](#reusable-methodology)
10. [Lessons Learned](#lessons-learned)

---

## What Are Claude Skills?

**Claude skills** are structured expert prompts that guide Claude Code to perform specific, complex analyses or generate standardized outputs. Think of them as "specialized consulting experts" that Claude can invoke when needed.

### Characteristics of Skills

- **Focused expertise**: Each skill addresses one specific problem domain (e.g., risk-based test planning)
- **Structured output**: Skills generate consistent, formatted results (e.g., traceability matrices, review reports)
- **Reusable**: Same skill can be applied across different tasks, projects, or contexts
- **Deterministic**: Same input should produce consistently structured output
- **Stored as prompts**: Skills are markdown files with expert instructions

### Example: Before and After Skills

**Before (Manual Process):**
```
You: "How should I test the database layer task?"
Claude: "You should write unit tests for the CRUD operations and integration
         tests with a real database. Aim for around 70% coverage."
```
- ❌ Vague coverage target (why 70%?)
- ❌ No risk assessment
- ❌ Missing edge cases
- ❌ Inconsistent across tasks

**After (With Skill):**
```
You: "Use the risk-based-test-planner skill for the database layer task."
Claude: [Generates comprehensive test strategy]
  - Risk Assessment: HIGH (data integrity + complexity)
  - Unit tests: 85-90% coverage (justified by risk level)
  - Integration tests: 100% of CRUD operations
  - Edge cases: Empty values, concurrent updates, DB failures, connection loss
  - Test data strategy: Factories for varied states, fixtures for edge cases
  - Effort estimate: 80-150% of implementation time (high risk multiplier)
```
- ✅ Risk-justified targets
- ✅ Comprehensive edge case identification
- ✅ Specific test data strategy
- ✅ Consistent across all tasks

---

## Skills vs. Agents: Key Differences

Understanding the distinction between skills and agents is crucial:

| Aspect | Skills | Agents |
|--------|--------|--------|
| **What they are** | Expert analysis prompts | Autonomous task executors |
| **Invocation** | Claude invokes them (or you ask Claude to) | You launch them explicitly |
| **Tools available** | None (analysis only) | Read, Write, Edit, Bash, etc. |
| **Execution** | Synchronous (immediate output) | Asynchronous (run independently) |
| **Output** | Structured analysis, recommendations | Code changes, files created, tasks completed |
| **Best for** | Planning, analysis, review, validation | Implementation, refactoring, testing, automation |
| **Example use** | "Analyze test coverage gaps" | "Implement the database layer" |

### When to Use What

**Use Skills for:**
- Planning and design (task decomposition, test strategies)
- Analysis and validation (traceability matrices, coverage gaps)
- Review and quality gates (code review, architecture compliance)
- Standardized output generation (reports, checklists, strategies)

**Use Agents for:**
- Code implementation (write features, refactor modules)
- Multi-step workflows (read files → analyze → make changes → test)
- Autonomous execution (implement a user story end-to-end)
- File manipulation and system operations

**Use Both Together:**
- Skill: Generate test strategy → Agent: Implement tests following strategy
- Skill: Review code quality → Agent: Fix identified issues
- Skill: Identify orphaned requirements → Agent: Implement missing features

---

## The Discovery Process

How did we discover which skills were needed for the Personal TaskBoard project? We used a **simulation-driven discovery** approach.

### Step 1: Understand the Context

We started by reviewing:
- **BMAd Process Documentation** (`docs/bmad-process.md`) - Our development methodology
- **Product Requirements Document** (`docs/prds/personal_task_manager.md`) - What we're building
- **Project Scale** - Large project (10 FRs, 11 NFRs, estimated 12 tasks)

**Key insight:** This project follows the BMAd Large Project flow, which has:
- Planning Phase: 6 steps
- Implementation Phase: 6 steps per task (12 tasks = 72 work units)
- Multiple quality gates and documentation requirements

### Step 2: Initial Brainstorming

We started with common pain points in software development:

**Planning Challenges:**
- Missing requirements during task breakdown
- Inconsistent task sizing
- Forgetting to create ADRs for architecture decisions

**Implementation Challenges:**
- Arbitrary test coverage targets
- Inconsistent code review standards
- Losing track of which requirements are implemented

**Initial Skill Ideas:**
1. `prd-to-architecture-decisions` - Extract open decisions from PRD
2. `acceptance-criteria-to-tests` - Generate test stubs from ACs
3. `typescript-strict-validator` - Check for strict mode violations
4. `component-hierarchy-planner` - Design component structure

These seemed useful, but were they the RIGHT skills? We needed validation.

### Step 3: Simulation-Driven Discovery

Instead of guessing, we **simulated actually implementing the PRD** using the BMAd process and documented every pain point.

---

## Simulation-Driven Discovery

This is the key methodology that led to high-impact skills.

### The Simulation Exercise

**Goal:** Walk through the entire BMAd Large Project flow for Personal TaskBoard and identify:
- Where manual work is repetitive
- Where decisions are subjective or inconsistent
- Where errors are likely to occur
- Where quality gates need enforcement

### Planning Phase Simulation

#### Step 1-2: PRD Creation & Approval ✓
Already have the PRD. No skill needed here.

#### Step 3: Create Solution Architecture

**What I had to do:**
- Address 13+ open decisions from PRD Section 6
- Write ADRs for: SQLite integration, component library, testing frameworks, state management, server/client component strategy, database location, etc.

**Pain Point Discovered:**
- 13 separate ADRs to write manually
- Easy to miss one or be inconsistent in format
- Format drift across multiple ADRs

**Skill Idea Generated:**
`open-decisions-to-adr-generator` - Extracts all open decisions from PRD and generates ADR templates

**Priority Assessment:** Medium-High (saves time, ensures consistency, but not blocking)

#### Step 5: Create High-Level Project File

**What I had to do:**
- Break PRD into implementable tasks
- Ensure all FRs and NFRs are covered
- Determine task dependencies
- Estimate effort per task

**Pain Points Discovered:**

**Pain Point 1: Manual Requirement Extraction**
- Had to manually scan PRD: "Did I cover FR1? FR2? ... FR10?"
- Had to track NFRs: "Does every task include NFR3 (TypeScript strict) and NFR4 (ESLint)?"
- Cross-cutting NFRs (like NFR5: 70% test coverage) apply to ALL tasks but easy to forget

**Pain Point 2: Task Boundary Decisions**
- Where does "Database Layer" end and "Business Logic" begin?
- Is "Task Creation" one task or should it be split?
- How granular should tasks be?

**Pain Point 3: Orphaned Requirements**
- What if I forget to create a task for FR8 (sorting)?
- How do I validate complete coverage?

**Skills Generated:**

1. **`prd-to-task-decomposition`** (Tier 1 - CRITICAL)
   - Why critical: Missing a requirement means incomplete product
   - What it does: Systematically extracts ALL FRs/NFRs and groups into logical tasks
   - Impact: Eliminates the #1 risk in planning phase

2. **`task-dependency-analyzer`** (Tier 3 - Nice to have)
   - Why nice to have: Dependencies are usually obvious ("database before UI")
   - What it does: Determines task ordering
   - Impact: Optimization, not risk reduction

3. **`complexity-estimator`** (Tier 3 - Nice to have)
   - Why nice to have: Estimates help planning but aren't blocking
   - What it does: Suggests effort per task
   - Impact: Better planning, not essential for quality

#### Step 6: Create High-Level Task Files (12 times!)

**What I had to do:**
For each of 12 tasks, create a markdown file with:
- Objectives
- High-level acceptance criteria
- Prerequisites and dependencies
- Success metrics

**Pain Point Discovered:**
- Repetitive structure (same sections, 12 times)
- Easy to forget a section or be inconsistent
- Copy-paste errors

**Skill Generated:**
`high-level-task-file-generator` (Tier 2) - Generates consistent task files from template

**Why Tier 2 not Tier 1?** Saves time and ensures consistency, but a human can do this manually without missing critical content.

#### Planning Phase Validation

**What I had to do:**
- Before starting implementation, validate that all FRs and NFRs are assigned to tasks
- Check for gaps or orphans

**Pain Point Discovered:**
- No systematic way to validate coverage
- Manual cross-referencing is error-prone
- As project evolves, hard to track which requirements are done

**Skill Generated:**
**`requirements-traceability-matrix`** (Tier 1 - CRITICAL)
- Why critical: Prevents shipping with missing features or untested requirements
- What it does: Tracks requirement → task → test coverage
- Impact: Continuous validation throughout project lifecycle

---

### Implementation Phase Simulation

Let me simulate **Task 2: Database Layer** (data-focused task) and **Task 6: Task Creation** (UI-focused task) to see patterns.

#### Task 2: Database Layer

**Step 1: Create Task (Detailed Story)**

**What I had to do:**
- Expand high-level task into detailed implementation story
- Extract data model from scattered FRs (title from FR1, note from FR1, status from FR2, timestamps from FR10)
- Determine schema (field types, constraints, indexes)
- Define acceptance criteria

**Pain Point Discovered:**
- Inferring data model from requirements is manual and error-prone
- "Did I miss a field? What about completed_at for FR6 (completion summary)?"
- Schema decisions (NOT NULL constraints, index choices) require thinking about all requirements

**Skill Generated:**
`requirements-to-data-model` (Tier 2) - Scans PRD and generates complete schema with justification

**Why Tier 2?** High value for data tasks, but not needed for every task (UI tasks don't need it).

**Step 2: Assemble Task Context**

**What I had to do:**
- Gather context for implementing the task
- For database task: SQLite best practices, Drizzle patterns (from ADR), migration strategies

**Pain Point Discovered:**
This is a **greenfield** project - no existing code to reference. But I still need context:
- What are Drizzle ORM best practices?
- How do I integrate SQLite with Next.js?
- What's the approved technology stack from ADRs?

Different task types need VERY different context:
- Data task: Schema patterns, query builders, migration strategies
- UI task: Component patterns, state management, interaction patterns
- API task: Server actions vs API routes (ADR decision), error handling

**Skill Generated:**
`task-type-detector-and-context-assembler` (Tier 1 - CRITICAL)
- Why critical: Wrong context leads to wrong implementation approach
- What it does: Detects task type and gathers type-appropriate patterns/ADRs
- Impact: Ensures implementation aligns with architecture from the start

**Step 3: Plan Test Coverage**

**What I had to do:**
Answer these questions:
- What test levels? (Unit? Integration? E2E?)
- What coverage target? (70%? 80%? 90%?)
- Which edge cases to test?
- What test data strategy? (Fixtures? Factories? Mocks?)

**Pain Point Discovered:**
This is HIGHLY subjective. Without a framework, I might say:
- "Database is important, let's aim for 90% coverage"
- "UI is just display, 60% is fine"

But what if the UI task has critical user workflows and the database task is simple CRUD? **Inconsistent risk assessment leads to under-testing critical paths and over-testing low-risk code.**

Also: What edge cases to test?
- For database: Empty title (FR1 says required), very long title, special characters, concurrent updates, DB connection failure
- Easy to miss: "Did I think of all edge cases?"

**Skill Generated:**
**`risk-based-test-planner`** (Tier 1 - CRITICAL)
- Why critical: Inconsistent test strategy is a quality risk; missing edge cases causes bugs
- What it does: Risk assessment (failure probability × impact) → justified coverage targets + edge case identification
- Impact: Consistent, comprehensive testing across all 12 tasks

**Step 4: Generate Acceptance Tests (ATDD)**

**What I had to do:**
Write failing tests for all acceptance criteria before implementing.

**Pain Point Discovered:**
- Tedious to write test structure for every CRUD operation
- Easy to forget assertions or test setup

**Skill Generated:**
`atdd-test-generator` (Tier 2) - Generates failing test suite from acceptance criteria

**Why Tier 2?** Saves time, but a developer can write tests manually without missing critical coverage (the test plan from Skill 3 guides them).

**Step 5: Implement Task**

**What I had to do:**
Write actual code following:
- Drizzle patterns from ADR
- TypeScript strict mode (NFR3)
- All tests passing
- Handle edge cases

**Pain Point Discovered:**
During implementation, easy to:
- Forget an ADR ("Wait, should I use server actions or API routes?")
- Use `any` types instead of proper TypeScript
- Skip edge case handling
- Deviate from established patterns

**Skill Generated:**
`adr-compliance-checker` (Tier 2) - Scans code for ADR violations in real-time

**Why Tier 2?** Helpful but not blocking; code review (Step 6) will catch violations.

**Step 6: Review Task**

**What I had to do:**
Review the implementation across multiple dimensions:
- ✅ Code quality (clean, maintainable)
- ✅ Architecture alignment (follows ADRs)
- ✅ Test coverage (meets target)
- ✅ Security (no vulnerabilities)
- ✅ Performance (meets NFRs)
- ✅ All acceptance criteria met
- ✅ TypeScript strict mode passing
- ✅ ESLint passing

**Pain Point Discovered:**
This is an **8+ point checklist**. Manual review is:
- Time-consuming (30-60 minutes per task × 12 tasks)
- Inconsistent (thorough on Task 1, rushed on Task 12)
- Subjective (what's "good enough" for code quality?)
- Easy to miss dimensions (forget to check performance, or skip security scan)

**Skill Generated:**
**`comprehensive-story-reviewer`** (Tier 1 - CRITICAL)
- Why critical: Quality gate enforcement; prevents architecture drift; ensures every task meets standards
- What it does: 7-dimension review (ACs, code quality, architecture, tests, security, performance, integration) with pass/fail
- Impact: Consistent quality across all 12 tasks; catches issues before integration

---

### Cross-Cutting Patterns Discovered

After simulating both data and UI tasks, I noticed:

**Pattern 1: Traceability is Essential**
- With 10 FRs + 11 NFRs across 12 tasks over 6 steps each (72 work units), tracking is critical
- Need to know: Which requirements are implemented? Which are tested? Any gaps?
- **Confirmed:** `requirements-traceability-matrix` is Tier 1

**Pattern 2: Task Type Matters**
- Data tasks need schema context; UI tasks need component patterns
- Test strategy differs (data = integration tests critical; UI = E2E tests critical)
- **Confirmed:** `task-type-detector-and-context-assembler` is Tier 1

**Pattern 3: Quality Degrades Over Time**
- By Task 12, easy to get sloppy or rushed
- Need automated quality gates
- **Confirmed:** `comprehensive-story-reviewer` is Tier 1

---

## Pain Point Analysis

From the simulation, we extracted **pain points** and categorized them:

### High-Risk Pain Points (Tier 1 Skills)

These cause **critical failures** if not addressed:

1. **Missing Requirements** (Planning Phase)
   - Pain: Forget to implement FR8 or NFR5
   - Impact: Incomplete product, failed acceptance
   - Skill: `prd-to-task-decomposition`

2. **Lost Traceability** (Throughout)
   - Pain: Don't know which requirements are done
   - Impact: Ship with gaps, can't validate coverage
   - Skill: `requirements-traceability-matrix`

3. **Inconsistent Test Strategy** (Implementation Phase)
   - Pain: Arbitrary coverage targets, missed edge cases
   - Impact: Under-tested critical paths, bugs in production
   - Skill: `risk-based-test-planner`

4. **Inconsistent Quality** (Implementation Phase)
   - Pain: Review thoroughness varies, forget to check security/performance
   - Impact: Architecture drift, vulnerabilities, technical debt
   - Skill: `comprehensive-story-reviewer`

### Time-Sink Pain Points (Tier 2 Skills)

These are **tedious and repetitive** but not critical failures:

5. **Repetitive Task File Creation** (Planning Phase)
   - Pain: Write 12 task files with same structure
   - Impact: Wastes time, minor inconsistencies
   - Skill: `high-level-task-file-generator`

6. **Manual Data Model Extraction** (Implementation Phase - Data Tasks)
   - Pain: Scan PRD to infer schema fields
   - Impact: Wastes time, might miss a field
   - Skill: `requirements-to-data-model`

### Nice-to-Have Pain Points (Tier 3 Skills)

These are **minor conveniences**:

7. **Manual ADR Writing** (Planning Phase)
   - Pain: Write 13 ADRs from scratch
   - Impact: Time-consuming, but straightforward
   - Skill: `open-decisions-to-adr-generator`

8. **Task Dependency Determination** (Planning Phase)
   - Pain: Figure out task ordering
   - Impact: Minor; dependencies are usually obvious
   - Skill: `task-dependency-analyzer`

---

## Skill Design Principles

Once we identified the pain points, we designed skills following these principles:

### Principle 1: Structured Expertise

Every skill is an **expert consultant** in a specific domain.

**Example: risk-based-test-planner**

Structure:
- **Core Expertise** - What the skill is expert in (risk assessment, test strategy)
- **Methodology** - Step-by-step process the skill follows
- **Output Format** - Exact structure of deliverable (test strategy document)
- **Quality Standards** - What makes a good output

This structure ensures:
- Claude knows exactly what role to play
- Output is consistent across invocations
- User knows what to expect

### Principle 2: Comprehensive Checklists

Skills don't rely on memory - they use **explicit checklists**.

**Example: comprehensive-story-reviewer**

Instead of "review the code quality", the skill has:
```markdown
### Code Quality Checklist
- [ ] Code is clean and well-organized
- [ ] Functions are appropriately sized and single-purpose
- [ ] Variable/function names are descriptive
- [ ] No code duplication (DRY principle)
- [ ] Comments explain "why", not "what"
- [ ] No dead code or commented-out blocks
- [ ] TypeScript types properly used (no unjustified `any`)
- [ ] Error handling is comprehensive
```

This ensures nothing is forgotten.

### Principle 3: Justified Recommendations

Skills don't just say "do X" - they explain **WHY**.

**Example: risk-based-test-planner**

Bad output:
```
Coverage target: 85%
```

Good output (from skill):
```
Coverage Target: 85-90%

Justification:
- Risk Level: HIGH (data integrity + query complexity)
- Failure Probability: MEDIUM (database operations, ORM complexity)
- Impact Severity: HIGH (data loss potential, user trust)
- Risk-based multiplier: High risk = 80-90% coverage target
```

This builds trust and helps users understand the reasoning.

### Principle 4: Actionable Specificity

Skills provide **file-level, line-level specificity**.

**Example: comprehensive-story-reviewer**

Bad feedback:
```
Code quality needs improvement.
```

Good feedback (from skill):
```
**Function too complex** (Severity: Medium)
- **Location:** `src/lib/taskManager.ts:45-78`
- **Description:** `updateTaskStatus` function has cyclomatic complexity of 12; recommended max is 10
- **Impact:** Harder to test and maintain
- **Recommendation:** Extract status validation to separate function
```

Users know exactly what to fix and where.

### Principle 5: Consistent Format

Skills generate **structured, repeatable output**.

Every skill has an "Output Format" section showing exact markdown structure. This ensures:
- Output is machine-parseable (could extract data programmatically)
- Output is human-readable (clear sections, tables, checklists)
- Output is consistent across tasks (Task 1 review looks like Task 12 review)

---

## Prioritization Framework

How did we decide which skills to build first?

### Prioritization Matrix

We scored each skill on two dimensions:

**Impact:** How much does this skill reduce risk or save time?
- **Critical:** Prevents major failures (missing requirements, untested code)
- **High:** Significant time savings or quality improvement
- **Medium:** Moderate value
- **Low:** Minor convenience

**Frequency:** How often is the skill used?
- **Every task:** 12 times in this project
- **Once per phase:** Planning phase + Implementation phase (2 times)
- **Once per project:** Just during initial planning

### Scoring Results

| Skill | Impact | Frequency | Score | Tier |
|-------|--------|-----------|-------|------|
| prd-to-task-decomposition | Critical | Once per project | Critical × 1 = **High** | 1 |
| requirements-traceability-matrix | Critical | Every phase + every task | Critical × Many = **Highest** | 1 |
| task-type-detector-and-context-assembler | Critical | Every task (12×) | Critical × 12 = **Highest** | 1 |
| risk-based-test-planner | Critical | Every task (12×) | Critical × 12 = **Highest** | 1 |
| comprehensive-story-reviewer | Critical | Every task (12×) | Critical × 12 = **Highest** | 1 |
| high-level-task-file-generator | High | Once per project (12 files) | High × 1 = **Medium** | 2 |
| requirements-to-data-model | High | Some tasks (3-4 data tasks) | High × 3 = **Medium** | 2 |
| atdd-test-generator | High | Every task (12×) | High × 12 = **Medium-High** | 2 |
| adr-compliance-checker | High | Every task (12×) | High × 12 = **Medium-High** | 2 |
| open-decisions-to-adr-generator | Medium | Once per project | Medium × 1 = **Low** | 3 |
| task-dependency-analyzer | Medium | Once per project | Medium × 1 = **Low** | 3 |

### Decision: Build Tier 1 First (Minimum Viable Skill Set)

We built the **5 Tier 1 skills** because they:
- Have critical impact (prevent major failures)
- Are used frequently (throughout project)
- Provide immediate value (can use them right away)

Tier 2 and 3 skills can be added later based on actual usage patterns.

---

## Our Skills: A Case Study

Let's deep-dive into one skill to show the design process.

### Case Study: risk-based-test-planner

#### Step 1: Identify the Pain Point

**Simulation finding:**
- Deciding test coverage targets is subjective
- Different developers might choose 60%, 70%, 80%, or 90% arbitrarily
- Easy to miss edge cases (concurrent updates, connection failures)
- No consistent framework for determining test strategy

**Impact:**
- Under-testing critical paths (database gets 60%, fails in production)
- Over-testing low-risk code (UI display logic gets 90%, wastes time)
- Missed edge cases cause bugs

#### Step 2: Define the Expert Role

**Who is this expert?**
- A Test Strategy Expert specializing in **risk-based test planning**
- Expertise: Risk assessment, test level selection, coverage target justification

**What's their framework?**
- Risk = Failure Probability × Impact Severity
- Higher risk → higher coverage targets
- Different task types need different test strategies

#### Step 3: Design the Methodology

The skill needs a **step-by-step process**:

1. **Categorize task type** (data layer, UI, API, etc.)
2. **Assess failure probability** (complexity, familiarity, dependencies)
3. **Assess impact severity** (user visibility, data integrity, security)
4. **Calculate risk level** (probability × severity)
5. **Recommend test levels** (unit, integration, E2E) based on risk
6. **Set coverage targets** (justified by risk level)
7. **Identify edge cases** (from requirements and task type)
8. **Recommend test data strategy** (factories, fixtures, mocks)
9. **Estimate test effort** (as % of implementation, based on risk)

#### Step 4: Create Structured Output Format

The skill generates:
```markdown
# Test Strategy: [Task Name]

## Risk Assessment
- Failure Probability: [Low/Medium/High] with justification
- Impact Severity: [Low/Medium/High] with justification
- Overall Risk Level: [Low/Medium/High/Critical]

## Test Level Recommendations
- Unit Testing: [Required/Optional] - Coverage: [%] - Justification
- Integration Testing: [Required/Optional] - Coverage: [%] - Justification
- E2E Testing: [Required/Optional] - Workflows: [list] - Justification

## Edge Cases and Boundary Conditions
1. [Edge case] - Test: [scenario] - Risk if untested: [impact]

## Test Data Strategy
- Approach: [Factories/Fixtures/Mocks]
- Justification: [why this approach]
- Example code: [code snippet]

## Success Criteria
- Coverage targets met: [checklist]
- Edge cases tested: [checklist]
- Quality gates passed: [checklist]
```

#### Step 5: Add Quality Standards

The skill includes:
- **Risk matrix** (2×2 grid: probability × severity)
- **Task type patterns** (data layer = high risk, infrastructure = low risk)
- **Coverage guidelines** (high risk = 80-90%, low risk = 60-70%)
- **Anti-patterns to avoid** (testing mocks only, ignoring edge cases)

#### Step 6: Test and Refine

We would test this skill on:
- Task 2: Database Layer (expect: HIGH risk, 85-90% coverage)
- Task 5: Task Board Display (expect: MEDIUM-HIGH risk, 70-80% + E2E)
- Task 1: Project Setup (expect: LOW risk, minimal testing)

Refine based on output quality.

---

## Reusable Methodology

Want to discover skills for your own project? Follow this process:

### Phase 1: Context Gathering

**1.1 Understand your process**
- What methodology do you follow? (Agile, BMAd, Waterfall, etc.)
- What are the phases and steps?
- Where are the quality gates?

**1.2 Understand your project**
- What are you building?
- What's the scale? (Small, Medium, Large)
- What are the key requirements and constraints?

### Phase 2: Simulation

**2.1 Simulate the entire workflow**
- Walk through your process end-to-end (on paper or in your mind)
- For each step, ask:
  - What do I need to do?
  - What decisions do I make?
  - What could go wrong?
  - What's repetitive or tedious?

**2.2 Document pain points**
For each pain point, capture:
- **What:** Description of the pain
- **When:** Which phase/step
- **Impact:** What happens if not addressed (risk level)
- **Frequency:** How often does this occur

**Example:**
- **What:** Determining test coverage targets
- **When:** Implementation Phase, Step 3 (every task)
- **Impact:** Under-testing causes bugs; over-testing wastes time
- **Frequency:** 12 times (every task)

### Phase 3: Skill Ideation

**3.1 For each pain point, ask:**
- Could an "expert consultant" solve this?
- What expertise would they need?
- What would their deliverable look like?
- Is this analysis/planning or execution?

**3.2 Design the skill concept**
- **Name:** `descriptive-skill-name`
- **Expert role:** "You are a [X] Expert specializing in [Y]"
- **Input:** What information does the skill need?
- **Output:** What does the skill produce?
- **Methodology:** Step-by-step process

### Phase 4: Prioritization

**4.1 Score each skill**
- **Impact:** Critical / High / Medium / Low
- **Frequency:** Every task / Per phase / Per project
- **Effort to build:** Hours / Days / Weeks

**4.2 Prioritize**
- **Tier 1 (Build first):** Critical impact + High frequency
- **Tier 2 (Build next):** High impact OR high frequency
- **Tier 3 (Build later):** Medium impact + low frequency

### Phase 5: Build and Test

**5.1 Write the skill prompt**
Follow design principles:
- Structured expertise (role + methodology)
- Comprehensive checklists (nothing forgotten)
- Justified recommendations (explain why)
- Actionable specificity (file-level details)
- Consistent format (structured output)

**5.2 Test the skill**
- Try it on a real example from your project
- Evaluate output quality
- Refine based on results

**5.3 Document**
- Add to README
- Provide usage examples
- Explain when to use

### Phase 6: Iterate

**6.1 Use the skills**
- Apply them in real development
- Note where they help and where they fall short

**6.2 Refine**
- Improve prompts based on actual usage
- Add missing dimensions
- Fix unclear instructions

**6.3 Add more skills**
- Build Tier 2 skills once Tier 1 is validated
- Build Tier 3 skills if they prove valuable

---

## Lessons Learned

### What Worked Well

**1. Simulation-driven discovery**
- Walking through the actual process revealed pain points we wouldn't have guessed
- Real examples (Task 2, Task 6) showed task-type differences
- Concrete pain points led to specific skill designs

**2. Prioritization framework**
- Impact × Frequency scoring made priorities clear
- Building Tier 1 first provided immediate value
- Can defer Tier 2/3 skills without risk

**3. Structured skill design**
- Following consistent principles made skills feel cohesive
- Comprehensive checklists prevent missed dimensions
- Structured output enables automation

### What We'd Do Differently

**1. Test skills earlier**
- We designed all 5 Tier 1 skills before testing any
- Better: Design one, test it, refine, then design the next
- Lesson: Build → Test → Refine → Repeat

**2. Start smaller**
- Could have started with just 2 skills (`prd-to-task-decomposition` + `requirements-traceability-matrix`)
- Validate value before building more
- Lesson: Minimum viable skill set could be even smaller

**3. Include examples in skills**
- Some skills are abstract; concrete examples would help
- Lesson: Add "Example Application" sections to each skill

### Common Pitfalls to Avoid

**1. Building skills for execution (use agents instead)**
- ❌ Bad: `implement-database-layer` skill (should be agent)
- ✅ Good: `plan-database-testing` skill (analysis/planning)

**2. Too narrow or too broad**
- ❌ Too narrow: `validate-empty-title-edge-case` (too specific)
- ❌ Too broad: `do-everything-for-task` (too vague)
- ✅ Just right: `risk-based-test-planner` (focused domain, clear output)

**3. Vague instructions**
- ❌ Bad: "Review the code quality"
- ✅ Good: 8-point checklist with specific criteria

**4. No justification**
- ❌ Bad: "Coverage target: 85%"
- ✅ Good: "Coverage target: 85% because risk level is HIGH (data integrity + complexity)"

---

## Conclusion

Claude skills are powerful tools for standardizing and accelerating development workflows. The key to discovering valuable skills is:

1. **Simulate** your actual workflow
2. **Document** pain points with impact and frequency
3. **Prioritize** by impact × frequency
4. **Design** skills with structured expertise and comprehensive checklists
5. **Test** and refine based on real usage

For the Personal TaskBoard project, we identified 17 potential skills and prioritized 5 as the **Minimum Viable Skill Set**:
- `prd-to-task-decomposition` - Ensures complete requirement coverage
- `requirements-traceability-matrix` - Tracks implementation progress
- `task-type-detector-and-context-assembler` - Ensures architecture alignment
- `risk-based-test-planner` - Justifies test strategy
- `comprehensive-story-reviewer` - Enforces quality gates

These 5 skills address the highest-risk pain points in the BMAd process and provide immediate value.

### Next Steps for Developers

**If you're new to Claude skills:**
1. Read the skills we created in `.claude/skills/`
2. Try using one: "Please use the prd-to-task-decomposition skill on my PRD"
3. Observe the structured output
4. Use the output in your development process

**If you want to create your own skills:**
1. Follow the **Reusable Methodology** in this document
2. Start with simulation-driven discovery
3. Build 1-2 Tier 1 skills first
4. Test, refine, then expand

**Remember:**
- Skills are for analysis and planning (use agents for execution)
- Start small, validate value, then expand
- Structured expertise beats ad-hoc prompting
- Consistency is the key benefit

Happy skill building!

---

**Document Version:** 1.0
**Last Updated:** 2025-10-21
**Status:** Complete - Ready for team review
