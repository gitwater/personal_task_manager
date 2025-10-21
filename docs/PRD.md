# Personal TaskBoard - Product Requirements Document (PRD)

**Project:** Personal TaskBoard
**Context:** AI-Accelerated SDLC Workshop (Greenfield App)
**Stack:** React + Next.js + SQLite
**Document Version:** v2.0

---

## 1. Goals and Background Context

### 1.1 Goals

- Capture informal, ad-hoc tasks that fall outside structured project management systems
- Provide a simple, personal workflow (To Do → Doing → Done) for tracking small commitments
- Enable users to maintain personal accountability and mental clarity
- Reduce task loss from Slack threads, email, and verbal conversations
- Deliver a sense of progress through visual task completion tracking

### 1.2 Background Context

Employees and managers frequently receive informal requests — quick reviews, reminders, and follow-ups — that don't belong in enterprise tools like Jira or Asana. These "out-of-band" tasks are easily forgotten or buried in communication channels, leading to missed commitments and mental overhead.

**Personal TaskBoard** addresses this gap by providing a lightweight, personal task manager optimized for individual accountability. Unlike team collaboration tools, this application focuses on personal organization, small wins, and the satisfaction of moving tasks through a simple three-stage workflow. The target user is any knowledge worker who needs to track informal commitments without the overhead of enterprise project management systems.

### 1.3 Change Log

| Date | Version | Description | Author |
|------|---------|-------------|--------|
| 2025-10-15 | v2.0 | Refactored to BMAD best practices; changed stack to Next.js; clarified technical assumptions | PM Agent |
| 2025-10-15 | v1.0 | Initial PRD creation | External Source |

---

## 2. Requirements

### 2.1 Functional Requirements

- **FR1**: Users can create a new task with a required title and optional note
- **FR2**: Users can view tasks organized in three columns: "To Do", "Doing", and "Done"
- **FR3**: Users can move a task between status columns
- **FR4**: Users can edit an existing task's title and note
- **FR5**: Users can delete a task
- **FR6**: Users can view a completion summary showing tasks completed today and this week
- **FR7**: All tasks persist to database and reload on application restart
- **FR8**: Task columns display tasks with most recent first
- **FR9**: Each task displays its title, note (if present), and relevant timestamp information
- **FR10**: System tracks when tasks are created, updated, and completed

### 2.2 Non-Functional Requirements

- **NFR1**: Application runs entirely locally (no external APIs or authentication)
- **NFR2**: Page load time < 300ms on local development environment
- **NFR3**: All code must pass TypeScript compilation with strict mode enabled
- **NFR4**: All code must pass ESLint validation before commit
- **NFR5**: Unit test coverage > 70% for business logic and utilities
- **NFR6**: E2E test coverage for all primary user workflows (add, move, edit, delete, persist)
- **NFR7**: Application must be maintainable with clean component architecture and typed contracts
- **NFR8**: UI must be responsive and usable on desktop browsers (1280px+ viewport minimum)
- **NFR9**: UI must use reusable component architecture with clear separation of concerns (architect must define component hierarchy)
- **NFR10**: Architect must define hydration strategy for server/client components appropriate to Next.js best practices
- **NFR11**: Components must be designed for reusability and composability to avoid duplication

---

## 3. User Interface Design Goals

### 3.1 Overall UX Vision

The application should feel **professional and credible** — suitable for use in a business environment. The user experience should prioritize **speed and clarity**: adding tasks should be fast, and the board state should be immediately comprehensible at a glance. The design should be clean and uncluttered.

### 3.2 Key Interaction Paradigms

- **Quick Capture**: Easy-to-find "Add Task" action
- **Direct Manipulation**: Tasks can be moved between columns (architect to determine interaction method)
- **Visual Feedback**: Smooth transitions and confirmations for user actions
- **Keyboard Navigation**: Basic keyboard support for primary actions

### 3.3 Core Screens and Views

- **Main Dashboard**: Three-column board (To Do | Doing | Done) with summary banner and "Add Task" action
- **Task Creation/Editing Interface**: Interface for creating and editing tasks
- **Single-page application** (no navigation between multiple screens)

### 3.4 Accessibility

- Basic keyboard navigation support
- Visible focus states for interactive elements

### 3.5 Branding

- **Visual Style**: Clean, professional, and neutral design suitable for a productivity tool
- **Color Palette**: Neutral foundation with a single accent color for actions
- **Typography**: Professional sans-serif; readable font sizes
- **Component Style**: Clear visual hierarchy with subtle shadows and rounded corners

### 3.6 Target Device and Platforms

**Web Responsive** — optimized for desktop browsers (1280px+ viewport), with graceful degradation for laptop screens (1024px+). Mobile support is out of scope for MVP.

---

## 4. Technical Assumptions

### 4.1 Repository Structure

**Monorepo** — Single repository containing all application code.

### 4.2 Service Architecture

**Monolithic Next.js Application** — All backend and frontend logic in one codebase.

### 4.3 Testing Requirements

- **Unit Tests**: Required for business logic and utilities
- **E2E Tests**: Required for all primary user workflows
- **Coverage**: >70% for unit tests; 100% coverage of user stories in E2E tests
- **Local Development Only**: No CI/CD pipeline; tests run locally

### 4.4 Technology Constraints

- **Stack**: Next.js + SQLite (architect to select specific tooling and frameworks)
- **TypeScript**: Strict mode enabled

---

## 5. Success Criteria & Metrics

### 5.1 MVP Completion Criteria

The MVP is considered complete when:

- **All Functional Requirements (FR1-FR10)** are implemented and operational
- **Testing Requirements (NFR5-NFR6)** are met:
  - Unit test coverage > 70% for business logic and utilities
  - E2E test coverage for all primary user workflows (add, move, edit, delete, persist)
- **Code Quality Gates (NFR3-NFR4)** pass:
  - TypeScript compilation with strict mode (no errors)
  - ESLint validation (no warnings or errors)
- **Application runs locally** without errors and persists data across restarts
- **All user workflows** can be completed end-to-end without manual intervention or workarounds

### 5.2 User Experience Success Indicators

While not hard gates for MVP completion, these indicators suggest successful implementation:

- **Task completion rate**: Users consistently move tasks from To Do → Doing → Done
- **Time-to-add-task**: < 5 seconds from clicking "Add Task" to task appearing on board
- **Zero data loss**: All tasks persist correctly across application restarts
- **Subjective satisfaction**: Application feels fast, clear, and professional

### 5.3 Acceptance Testing Scenarios

The following scenarios must be demonstrable for MVP acceptance:

1. Create 5 tasks, move them through workflow, close/reopen app → all tasks persist with correct status
2. Edit task title and note → changes save and display correctly
3. Delete task from any status → task removed and does not reappear on restart
4. View completion summary → accurate count of tasks completed today and this week
5. Add task with empty title → validation prevents creation
6. Move task from Done back to Doing → task status updates and timestamp reflects change

### 5.4 Key User Stories

The following user stories represent the primary user journeys and value propositions for the Personal TaskBoard MVP. These stories provide narrative context for the functional requirements and serve as a bridge between user needs and implementation.

**Story Format**: As a [user type], I want to [action/capability] so that [benefit/outcome]

---

#### **US1: Quick Task Capture**
**As a** knowledge worker receiving informal requests via Slack, email, or conversation
**I want to** quickly create a task with a title and optional note
**So that** I don't lose track of commitments that fall outside my formal project management tools

**Acceptance Criteria:**
- Task creation takes less than 5 seconds
- Title is required; note is optional
- New tasks appear in "To Do" column immediately
- Related to: FR1, FR7

---

#### **US2: Visual Workflow Organization**
**As a** user managing multiple small commitments
**I want to** see my tasks organized in three columns (To Do, Doing, Done)
**So that** I can understand my current workload and progress at a glance

**Acceptance Criteria:**
- All three columns visible on main dashboard
- Most recent tasks appear first in each column
- Column layout is clear and uncluttered
- Related to: FR2, FR8

---

#### **US3: Task Progression**
**As a** user working through my commitments
**I want to** move tasks between status columns as I start and complete work
**So that** I can track my progress and maintain accountability

**Acceptance Criteria:**
- Tasks can move from To Do → Doing → Done
- Tasks can move backward (e.g., Done → Doing) if needed
- Status changes are reflected immediately
- Related to: FR3, FR10

---

#### **US4: Task Detail Management**
**As a** user who needs to track context and updates
**I want to** edit a task's title and note after creation
**So that** I can update details as requirements evolve or I learn more information

**Acceptance Criteria:**
- Can edit both title and note fields
- Changes save and display immediately
- Edit history tracked via timestamps
- Related to: FR4, FR9, FR10

---

#### **US5: Task Removal**
**As a** user maintaining a clean workspace
**I want to** delete tasks that are no longer relevant or were created by mistake
**So that** my board stays focused on current commitments

**Acceptance Criteria:**
- Tasks can be deleted from any status column
- Deletion is permanent (confirmation recommended)
- Deleted tasks do not reappear on restart
- Related to: FR5

---

#### **US6: Progress Visibility**
**As a** user seeking motivation and accountability
**I want to** see a summary of tasks I've completed today and this week
**So that** I feel a sense of accomplishment and can demonstrate my follow-through

**Acceptance Criteria:**
- Summary visible on main dashboard
- Shows count of tasks completed today
- Shows count of tasks completed this week
- Updates in real-time as tasks move to Done
- Related to: FR6, FR10

---

#### **US7: Persistent Personal Workspace**
**As a** user relying on this tool for accountability
**I want to** have all my tasks persist across application restarts
**So that** I never lose track of my commitments due to technical issues

**Acceptance Criteria:**
- All task data saved to local database
- Tasks reload with correct status on app restart
- Zero data loss across sessions
- Timestamps preserved accurately
- Related to: FR7, FR10

---

**User Journey Summary:**

The typical user workflow follows this pattern:
1. **Capture** → User receives informal request and quickly adds task to "To Do"
2. **Organize** → User views board and prioritizes work
3. **Progress** → User moves task to "Doing" when starting work
4. **Complete** → User moves task to "Done" when finished
5. **Review** → User checks completion summary for sense of progress
6. **Maintain** → User edits or deletes tasks as needed throughout the day

This workflow repeats throughout the workday and across multiple days, with the application serving as a persistent, personal accountability tool.

---

## 6. Open Technical Decisions (For Architect)

The following decisions are intentionally left to the architect/implementation team, as they involve technical tradeoffs appropriate to Next.js and React best practices:

### 6.1 Interaction Patterns

- **Task Movement**: Drag-and-drop, dropdown selector, or button-based status changes?
- **Task Creation**: Modal dialog, inline form, or slide-out panel?
- **Task Editing**: Click-to-edit inline, dedicated edit view, or modal?

### 6.2 Technical Implementation

- **SQLite Integration**: ORM (Prisma, Drizzle) vs. query builder vs. raw SQL?
- **Component Library**: shadcn/ui, Material UI, Tailwind-only, or custom components?
- **Testing Frameworks**: Jest + React Testing Library? Vitest? Playwright vs. Cypress for E2E?
- **State Management**: React Context, Zustand, Jotai, server state only, or none (rely on Next.js server components)?
- **Server/Client Component Strategy**: What should be server components vs. client components? Where is the hydration boundary?
- **Database Location**: SQLite file location and initialization strategy

### 6.3 UI/UX Implementation Details

- **Column Layout**: Fixed widths, flex layout, or CSS grid?
- **Task Cards**: Card component structure, spacing, and visual hierarchy
- **Animation/Transitions**: What transitions for task movement and status changes?
- **Loading States**: How to handle async operations (optimistic updates vs. loading indicators)?
- **Empty States**: What to show when columns have no tasks?

### 6.4 Code Organization

- **Folder Structure**: Feature-based, type-based (components/lib/app), or hybrid?
- **API Routes**: Next.js API routes, server actions, or direct database access in server components?
- **Type Definitions**: Location and organization of TypeScript types/interfaces
- **Component Hierarchy**: Atomic design, feature-based components, or flat structure?

**Note to Architect**: These decisions should be documented in an Architecture Decision Record (ADR) or Technical Design Document (TDD) that references this PRD.

---

## 7. Out of Scope (for MVP)

* Multi-user collaboration or task sharing
* Push notifications or email reminders
* User authentication or account management
* AI-powered task prioritization or summarization
* Task categories, tags, or labels
* Task due dates or calendar integration
* Mobile applications (iOS/Android native)
* Cloud synchronization or backup
* Task search functionality (future enhancement)
* Recurring tasks or templates
