# Personal TaskBoard - Solution Architecture

**Project:** Personal TaskBoard
**Date:** 2025-10-21
**Author:** Kevin
**Version:** 1.0
**Status:** Complete

---

## 1. Executive Summary

### 1.1 Project Overview

Personal TaskBoard is a Level 1 greenfield web application designed to help knowledge workers track informal, ad-hoc tasks that fall outside structured project management systems. The application provides a simple three-column workflow (To Do → Doing → Done) with local-only persistence, optimized for speed and personal accountability.

**Key Characteristics:**
- **Scope**: Simple CRUD task management with status tracking
- **Scale**: Single-user, local-only deployment (no authentication, no cloud sync)
- **Complexity**: Level 1 (straightforward implementation, ~2-3 week timeline)
- **Architecture Style**: Monolithic Next.js application
- **Repository Strategy**: Monorepo

### 1.2 Architecture Approach

This architecture leverages **Next.js 15 App Router with React Server Components** for optimal performance and developer experience. The application uses:

- **Server Components** for data fetching and initial rendering (fast page loads)
- **Client Components** for interactive UI elements (drag-drop, forms, animations)
- **Server Actions** for mutations (create, update, delete tasks)
- **SQLite with Drizzle ORM** for local persistence
- **shadcn/ui + Tailwind CSS** for professional, accessible UI components

**Architecture Decision Rationale:**
- Monolithic structure appropriate for Level 1 project scope
- Server Components reduce client-side JavaScript bundle
- Server Actions eliminate need for API routes for simple mutations
- Drizzle ORM provides type-safe database access with minimal overhead
- Local SQLite removes deployment complexity while meeting all requirements

### 1.3 Success Criteria

- Page load time < 300ms (NFR2)
- Task creation < 5 seconds (US1)
- Zero data loss across restarts (FR7, US7)
- >70% unit test coverage (NFR5)
- 100% E2E coverage of user stories (NFR6)
- TypeScript strict mode compilation (NFR3)
- ESLint validation passing (NFR4)

---

## 2. Technology Stack and Decisions

### 2.1 Technology and Library Decisions

| Category | Technology | Version | Rationale |
|----------|------------|---------|-----------|
| **Framework** | Next.js | 15.0.3 | App Router with Server Components, optimal for data-driven UI, excellent TypeScript support |
| **Runtime** | Node.js | 20.x LTS | Stable LTS, required for Next.js 15 |
| **UI Library** | React | 19.0.0 | Included with Next.js 15, modern concurrent features |
| **Language** | TypeScript | 5.7.2 | Strict mode enabled, type safety for entire codebase |
| **Database** | SQLite | better-sqlite3 11.5.0 | Local-only persistence, zero-config, ACID transactions, synchronous API |
| **ORM** | Drizzle ORM | 0.36.4 | Type-safe, lightweight, excellent DX, works well with SQLite |
| **Styling** | Tailwind CSS | 3.4.17 | Utility-first, rapid development, small bundle with tree-shaking |
| **Component Library** | shadcn/ui | latest | Copy-paste components (not NPM), built on Radix UI, full customization |
| **UI Primitives** | Radix UI | 1.1.2 | Accessible, unstyled components (via shadcn/ui), keyboard navigation |
| **Icons** | Lucide React | 0.468.0 | Consistent icon set, tree-shakeable, TypeScript-native |
| **Form Validation** | Zod | 3.24.1 | Schema validation for forms, integrates with TypeScript |
| **Date/Time** | date-fns | 4.1.0 | Lightweight, tree-shakeable, functional API for timestamp formatting |
| **Unit Testing** | Vitest | 3.1.4 | Fast, Vite-powered, excellent TypeScript support, Jest-compatible API |
| **UI Testing** | Testing Library | @testing-library/react 16.1.0 | User-centric testing, accessibility-focused |
| **E2E Testing** | Playwright | 1.49.1 | Cross-browser, reliable, excellent DX, screenshots/videos |
| **Linting** | ESLint | 9.17.0 | Code quality, Next.js config, TypeScript rules |
| **Code Formatting** | Prettier | 3.4.2 | Consistent code style, integrates with ESLint |
| **Type Checking** | typescript-eslint | 8.18.2 | ESLint + TypeScript integration |

### 2.2 Rationale for Key Technology Choices

**Next.js 15 App Router:**
- Server Components reduce client JavaScript (faster load times)
- Built-in optimization (image optimization, font optimization)
- Server Actions eliminate API boilerplate for mutations
- File-based routing for simple navigation structure
- Excellent TypeScript integration

**SQLite + Drizzle ORM:**
- SQLite: Local-only requirement met, no server needed, ACID compliance
- Drizzle: Type-safe queries, minimal runtime overhead, excellent migrations
- Alternative considered: Prisma (heavier, slower startup, overkill for this project)

**shadcn/ui + Tailwind CSS:**
- shadcn/ui: Own the code, full customization, no bloat
- Radix UI primitives: Accessibility built-in (keyboard nav, ARIA, focus management)
- Tailwind: Rapid development, Illustrate Inc. brand colors easily configured
- Alternative considered: Material UI (heavier, less flexible, slower)

**Vitest + Playwright:**
- Vitest: Fast, modern, excellent TypeScript DX, Vite-powered
- Playwright: Reliable E2E, cross-browser, better debugging than Cypress
- Alternative considered: Jest (slower, requires more config)

---

## 3. Repository and Service Architecture

### 3.1 Repository Strategy

**Monorepo** - Single repository containing all application code.

```
personal_task_manager/
├── src/                    # Application source code
├── public/                 # Static assets
├── docs/                   # Documentation (PRD, UX spec, this doc)
├── tests/                  # E2E tests (Playwright)
├── package.json            # Dependencies and scripts
├── tsconfig.json           # TypeScript configuration
├── tailwind.config.ts      # Tailwind configuration
├── drizzle.config.ts       # Drizzle ORM configuration
└── next.config.js          # Next.js configuration
```

**Rationale:**
- Simple project structure for Level 1 application
- All code in one place (easier development, testing, deployment)
- No need for polyrepo complexity (no microservices, no separate frontend/backend)

### 3.2 Service Architecture

**Monolithic Next.js Application** - All backend and frontend logic in one codebase.

```
┌─────────────────────────────────────────┐
│         Next.js Application             │
│                                         │
│  ┌─────────────────────────────────┐   │
│  │   Client Components (Browser)   │   │
│  │   - TaskBoard (hydrated)        │   │
│  │   - TaskForm (interactive)      │   │
│  │   - DragDrop handlers           │   │
│  └─────────────────────────────────┘   │
│              ↕ Hydration                │
│  ┌─────────────────────────────────┐   │
│  │   Server Components (Server)    │   │
│  │   - TaskList (data fetch)       │   │
│  │   - CompletionSummary (calc)    │   │
│  └─────────────────────────────────┘   │
│              ↕ Server Actions           │
│  ┌─────────────────────────────────┐   │
│  │   Data Layer (Drizzle ORM)      │   │
│  │   - Task CRUD operations        │   │
│  │   - Queries (get tasks by status)│  │
│  └─────────────────────────────────┘   │
│              ↕ SQL                      │
│  ┌─────────────────────────────────┐   │
│  │   SQLite Database (File)        │   │
│  │   - tasks.db                    │   │
│  └─────────────────────────────────┘   │
└─────────────────────────────────────────┘
```

**Interaction Flow:**
1. User navigates to `/` → Next.js Server Component renders
2. Server fetches tasks from SQLite via Drizzle ORM
3. Server-rendered HTML sent to browser (fast initial load)
4. Client Components hydrate for interactivity
5. User creates task → Client Component calls Server Action
6. Server Action validates → writes to SQLite → revalidates
7. UI updates with new task (optimistic or refresh)

**No API Routes Required:**
- Server Actions handle all mutations (create, update, delete)
- Server Components handle all queries (fetch tasks)
- Simpler than traditional API routes
- Type-safe end-to-end (TypeScript from UI to database)

---

## 4. System Architecture

### 4.1 High-Level Component Architecture

```
┌──────────────────────────────────────────────────────────┐
│                     Browser (Client)                      │
│  ┌────────────────────────────────────────────────────┐  │
│  │  App Shell (Layout)                                │  │
│  │  - Header (CompletionSummary)                      │  │
│  │  - Main Content                                    │  │
│  └────────────────────────────────────────────────────┘  │
│                         │                                 │
│  ┌────────────────────────────────────────────────────┐  │
│  │  TaskBoard Component (Client)                      │  │
│  │  - Manages drag-drop state                         │  │
│  │  - Handles task creation modal                     │  │
│  │  - Coordinates Server Actions                      │  │
│  └────────────────────────────────────────────────────┘  │
│         │                  │                  │           │
│  ┌──────────┐      ┌──────────┐      ┌──────────┐       │
│  │ TaskCol  │      │ TaskCol  │      │ TaskCol  │       │
│  │ (To Do)  │      │ (Doing)  │      │  (Done)  │       │
│  └──────────┘      └──────────┘      └──────────┘       │
│     │                 │                 │                │
│  ┌────────┐       ┌────────┐       ┌────────┐          │
│  │TaskCard│       │TaskCard│       │TaskCard│          │
│  └────────┘       └────────┘       └────────┘          │
└──────────────────────────────────────────────────────────┘
                         ↕
              Server Actions / RSC
                         ↕
┌──────────────────────────────────────────────────────────┐
│                   Next.js Server                          │
│  ┌────────────────────────────────────────────────────┐  │
│  │  Server Actions                                    │  │
│  │  - createTask(data)                                │  │
│  │  - updateTask(id, data)                            │  │
│  │  - deleteTask(id)                                  │  │
│  │  - updateTaskStatus(id, status)                    │  │
│  └────────────────────────────────────────────────────┘  │
│                         ↕                                 │
│  ┌────────────────────────────────────────────────────┐  │
│  │  Data Access Layer (Drizzle ORM)                   │  │
│  │  - db.query.tasks.findMany()                       │  │
│  │  - db.insert(tasks).values(...)                    │  │
│  │  - db.update(tasks).set(...)                       │  │
│  │  - db.delete(tasks).where(...)                     │  │
│  └────────────────────────────────────────────────────┘  │
│                         ↕                                 │
│  ┌────────────────────────────────────────────────────┐  │
│  │  SQLite Database                                   │  │
│  │  - tasks table (id, title, note, status, ...)     │  │
│  │  - File: ./data/tasks.db                           │  │
│  └────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────┘
```

### 4.2 Server vs. Client Component Strategy

**Server Components (Default):**
- Page layouts (`app/layout.tsx`, `app/page.tsx`)
- Data fetching components (initial task list)
- Static content (headers, footers)
- CompletionSummary calculation (if server-rendered)

**Client Components (`'use client'`):**
- TaskBoard (manages interactive state)
- TaskForm (form inputs, validation)
- TaskCard (drag-drop, hover states, click handlers)
- Any component using React hooks (useState, useEffect)
- Any component with event handlers

**Hydration Boundary:**
```typescript
// app/page.tsx (Server Component)
export default async function HomePage() {
  const tasks = await getTasks(); // Server-side data fetch

  return <TaskBoard initialTasks={tasks} />; // Hydrates on client
}

// components/TaskBoard.tsx (Client Component)
'use client';
export function TaskBoard({ initialTasks }: { initialTasks: Task[] }) {
  const [tasks, setTasks] = useState(initialTasks); // Client state
  // ... interactive logic
}
```

### 4.3 State Management Strategy

**No Global State Library Required** - Use React Server Components + Server Actions

**State Distribution:**
1. **Server State (Source of Truth)**: SQLite database
2. **Server Component State**: Fetched on each request, passed to client
3. **Client Component State**: Local UI state (form inputs, drag state, modals)
4. **Optimistic Updates**: Optional (update UI before server confirms)

**State Flow:**
```
Database → Server Component (fetch) → Client Component (initialTasks prop)
                                    ↓
                            useState(initialTasks)
                                    ↓
                          User Interaction (create/update/delete)
                                    ↓
                          Server Action (mutation)
                                    ↓
                          Database Update + Revalidate
                                    ↓
                          Server Component Re-renders
                                    ↓
                          Client Component Receives New Props
```

**Alternatives Considered:**
- ❌ Zustand/Jotai: Overkill for simple app, adds complexity
- ❌ React Context: Unnecessary with Server Components
- ✅ Server Components + Server Actions: Simplest, type-safe, no extra dependencies

---

## 5. Data Architecture

### 5.1 Database Schema

**Single Table: `tasks`**

```sql
CREATE TABLE tasks (
  id            TEXT PRIMARY KEY,           -- UUID v4
  title         TEXT NOT NULL,              -- Max 200 characters (enforced in app)
  note          TEXT,                       -- Optional, max 2000 characters
  status        TEXT NOT NULL DEFAULT 'todo', -- 'todo' | 'doing' | 'done'
  created_at    INTEGER NOT NULL,           -- Unix timestamp (milliseconds)
  updated_at    INTEGER NOT NULL,           -- Unix timestamp (milliseconds)
  completed_at  INTEGER,                    -- Unix timestamp when moved to 'done'

  CHECK (status IN ('todo', 'doing', 'done'))
);

-- Indexes for common queries
CREATE INDEX idx_tasks_status ON tasks(status);
CREATE INDEX idx_tasks_created_at ON tasks(created_at DESC);
CREATE INDEX idx_tasks_completed_at ON tasks(completed_at DESC) WHERE completed_at IS NOT NULL;
```

**Drizzle ORM Schema:**

```typescript
// src/db/schema.ts
import { sqliteTable, text, integer, index } from 'drizzle-orm/sqlite-core';

export const tasks = sqliteTable('tasks', {
  id: text('id').primaryKey(),
  title: text('title').notNull(),
  note: text('note'),
  status: text('status', { enum: ['todo', 'doing', 'done'] }).notNull().default('todo'),
  createdAt: integer('created_at', { mode: 'timestamp_ms' }).notNull(),
  updatedAt: integer('updated_at', { mode: 'timestamp_ms' }).notNull(),
  completedAt: integer('completed_at', { mode: 'timestamp_ms' }),
}, (table) => ({
  statusIdx: index('idx_tasks_status').on(table.status),
  createdAtIdx: index('idx_tasks_created_at').on(table.createdAt),
  completedAtIdx: index('idx_tasks_completed_at').on(table.completedAt),
}));

export type Task = typeof tasks.$inferSelect;
export type NewTask = typeof tasks.$inferInsert;
```

### 5.2 Data Model

**Task Entity:**

```typescript
interface Task {
  id: string;                  // UUID v4 (generated on creation)
  title: string;               // Required, 1-200 characters
  note?: string;               // Optional, 0-2000 characters
  status: 'todo' | 'doing' | 'done';
  createdAt: Date;             // Auto-set on creation
  updatedAt: Date;             // Auto-updated on modification
  completedAt?: Date;          // Set when status changes to 'done'
}
```

**Validation Rules:**
- `title`: Required, non-empty, trimmed, max 200 chars
- `note`: Optional, trimmed, max 2000 chars
- `status`: Must be one of 'todo', 'doing', 'done'
- `id`: Auto-generated UUID v4
- `createdAt`: Auto-set to current timestamp
- `updatedAt`: Auto-updated on every modification
- `completedAt`: Set when status becomes 'done', cleared if moved back

### 5.3 Database Location and Initialization

**Database File Location:**
```
project_root/
└── data/
    └── tasks.db          # SQLite database file
```

**Initialization Strategy:**
1. On first run, check if `./data/tasks.db` exists
2. If not, create directory and database file
3. Run Drizzle migrations to create schema
4. Optionally seed with sample tasks for development

**Migration Management:**
- Use Drizzle Kit for schema migrations
- Migrations stored in `./drizzle/` directory
- Run migrations on application startup (development)
- Manual migration for production (if deployed)

**Connection Configuration:**

```typescript
// src/db/index.ts
import Database from 'better-sqlite3';
import { drizzle } from 'drizzle-orm/better-sqlite3';
import * as schema from './schema';

const sqlite = new Database('./data/tasks.db');
export const db = drizzle(sqlite, { schema });
```

### 5.4 Data Access Patterns

**Common Queries:**

```typescript
// Get all tasks grouped by status
async function getAllTasks() {
  return db.query.tasks.findMany({
    orderBy: (tasks, { desc }) => [desc(tasks.createdAt)],
  });
}

// Get tasks by status
async function getTasksByStatus(status: 'todo' | 'doing' | 'done') {
  return db.query.tasks.findMany({
    where: (tasks, { eq }) => eq(tasks.status, status),
    orderBy: (tasks, { desc }) => [desc(tasks.createdAt)],
  });
}

// Get completion counts
async function getCompletionCounts() {
  const now = new Date();
  const todayStart = startOfDay(now).getTime();
  const weekStart = startOfWeek(now, { weekStartsOn: 1 }).getTime();

  const tasks = await db.query.tasks.findMany({
    where: (tasks, { and, gte, eq }) =>
      and(
        eq(tasks.status, 'done'),
        gte(tasks.completedAt, weekStart)
      ),
  });

  const completedToday = tasks.filter(t => t.completedAt! >= todayStart).length;
  const completedThisWeek = tasks.length;

  return { completedToday, completedThisWeek };
}
```

**Mutations:**

```typescript
// Create task
async function createTask(input: { title: string; note?: string }) {
  const newTask: NewTask = {
    id: crypto.randomUUID(),
    title: input.title.trim(),
    note: input.note?.trim(),
    status: 'todo',
    createdAt: new Date(),
    updatedAt: new Date(),
  };

  await db.insert(tasks).values(newTask);
  return newTask;
}

// Update task
async function updateTask(id: string, input: { title?: string; note?: string }) {
  await db.update(tasks)
    .set({
      ...input,
      updatedAt: new Date(),
    })
    .where(eq(tasks.id, id));
}

// Update task status
async function updateTaskStatus(id: string, newStatus: Task['status']) {
  const updates: Partial<Task> = {
    status: newStatus,
    updatedAt: new Date(),
  };

  if (newStatus === 'done') {
    updates.completedAt = new Date();
  } else {
    updates.completedAt = null; // Clear if moved back from done
  }

  await db.update(tasks)
    .set(updates)
    .where(eq(tasks.id, id));
}

// Delete task
async function deleteTask(id: string) {
  await db.delete(tasks).where(eq(tasks.id, id));
}
```

---

## 6. API and Interface Design

### 6.1 Server Actions (Mutations)

Server Actions replace traditional API routes for mutations. They're defined in server files and called from client components.

**Task Server Actions:**

```typescript
// src/app/actions.ts
'use server';

import { revalidatePath } from 'next/cache';
import { z } from 'zod';
import { createTask, updateTask, deleteTask, updateTaskStatus } from '@/db/queries';

// Validation schemas
const createTaskSchema = z.object({
  title: z.string().min(1, 'Title is required').max(200),
  note: z.string().max(2000).optional(),
});

const updateTaskSchema = z.object({
  title: z.string().min(1).max(200).optional(),
  note: z.string().max(2000).optional(),
});

// Actions
export async function createTaskAction(formData: FormData) {
  const input = createTaskSchema.parse({
    title: formData.get('title'),
    note: formData.get('note'),
  });

  await createTask(input);
  revalidatePath('/'); // Refresh page data

  return { success: true };
}

export async function updateTaskAction(id: string, formData: FormData) {
  const input = updateTaskSchema.parse({
    title: formData.get('title'),
    note: formData.get('note'),
  });

  await updateTask(id, input);
  revalidatePath('/');

  return { success: true };
}

export async function updateTaskStatusAction(id: string, status: 'todo' | 'doing' | 'done') {
  await updateTaskStatus(id, status);
  revalidatePath('/');

  return { success: true };
}

export async function deleteTaskAction(id: string) {
  await deleteTask(id);
  revalidatePath('/');

  return { success: true };
}
```

**Usage in Client Components:**

```typescript
// components/TaskForm.tsx
'use client';

import { createTaskAction } from '@/app/actions';
import { useFormStatus } from 'react-dom';

export function TaskForm() {
  async function handleSubmit(formData: FormData) {
    const result = await createTaskAction(formData);
    if (result.success) {
      // Close modal, show success message
    }
  }

  return (
    <form action={handleSubmit}>
      <input name="title" required maxLength={200} />
      <textarea name="note" maxLength={2000} />
      <button type="submit">Add Task</button>
    </form>
  );
}
```

### 6.2 Data Fetching (Queries)

Server Components fetch data directly, no API routes needed.

```typescript
// app/page.tsx (Server Component)
import { getAllTasks, getCompletionCounts } from '@/db/queries';
import { TaskBoard } from '@/components/TaskBoard';
import { CompletionSummary } from '@/components/CompletionSummary';

export default async function HomePage() {
  const [tasks, counts] = await Promise.all([
    getAllTasks(),
    getCompletionCounts(),
  ]);

  return (
    <main>
      <CompletionSummary
        completedToday={counts.completedToday}
        completedThisWeek={counts.completedThisWeek}
      />
      <TaskBoard initialTasks={tasks} />
    </main>
  );
}
```

### 6.3 Type Safety

**End-to-End Type Safety:**

```
Database Schema (Drizzle)
    ↓ (inferred types)
Task Type
    ↓ (validated with Zod)
Server Actions (input)
    ↓ (TypeScript props)
Client Components
    ↓ (TypeScript events)
User Interactions
```

**Benefits:**
- Compile-time errors if schema changes
- Autocomplete in IDE for all database fields
- No manual type definitions needed
- Validation at runtime with Zod

---

## 7. Cross-Cutting Concerns

### 7.1 Error Handling

**Strategy:**
- Server Actions return `{ success: boolean; error?: string }`
- Client components display error messages in UI
- Database errors caught and logged server-side
- Validation errors shown inline in forms

**Error Boundaries:**
```typescript
// app/error.tsx
'use client';

export default function Error({ error, reset }: {
  error: Error;
  reset: () => void;
}) {
  return (
    <div>
      <h2>Something went wrong!</h2>
      <p>{error.message}</p>
      <button onClick={reset}>Try again</button>
    </div>
  );
}
```

**Form Validation Errors:**
- Zod validation in Server Actions
- Return errors to client for display
- Inline error messages below fields

### 7.2 Logging

**Development Logging:**
- Console logs for debugging
- Next.js dev server logs requests

**Production Logging (Future):**
- Structured logging with pino or winston
- Log database operations, errors, performance metrics
- File-based logs in `./logs/` directory

**Current Approach:**
```typescript
// Simple logging utility
export const logger = {
  info: (msg: string, meta?: any) => console.log(`[INFO] ${msg}`, meta),
  error: (msg: string, meta?: any) => console.error(`[ERROR] ${msg}`, meta),
  warn: (msg: string, meta?: any) => console.warn(`[WARN] ${msg}`, meta),
};
```

### 7.3 Performance Optimization

**Server Components:**
- Reduce client-side JavaScript bundle
- Fetch data close to database (no network latency)
- Stream HTML to browser (fast initial render)

**Image Optimization:**
- Use Next.js `<Image>` component (if images added in future)
- Automatic WebP conversion, lazy loading

**Code Splitting:**
- Automatic with Next.js App Router
- Each route bundles only what it needs
- Dynamic imports for modals/dialogs

**Caching:**
- Next.js automatic caching of Server Component renders
- `revalidatePath()` after mutations to refresh data
- No need for manual cache invalidation

**Performance Targets:**
- Page load: < 300ms (NFR2)
- Task creation: < 5s (US1 acceptance criteria)
- Status change: < 100ms perceived (smooth animations)

### 7.4 Accessibility

**Compliance Target:** WCAG 2.1 Level A (MVP)

**Implementation:**
- shadcn/ui components have built-in accessibility (Radix UI)
- Keyboard navigation support (Tab, Enter, Escape, Arrow keys)
- Focus indicators on all interactive elements
- ARIA labels and semantic HTML
- Screen reader announcements for state changes

**Key Features:**
- Keyboard shortcuts (defined in UX spec)
- Focus trap in modals
- Focus return after modal close
- Visible focus rings (2px blue, per UX spec)
- Color contrast WCAG AA compliant

**Testing:**
- Manual keyboard navigation testing
- axe DevTools browser extension
- Lighthouse accessibility audit

---

## 8. Component Architecture and Integration

### 8.1 Component Hierarchy

```
App (app/layout.tsx) - Server Component
└── Page (app/page.tsx) - Server Component
    ├── Header
    │   └── CompletionSummary - Server Component (or Client for real-time updates)
    └── Main
        └── TaskBoard - Client Component
            ├── TaskColumn (To Do) - Client Component
            │   ├── AddTaskButton
            │   └── TaskCard[] - Client Component
            ├── TaskColumn (Doing) - Client Component
            │   └── TaskCard[] - Client Component
            └── TaskColumn (Done) - Client Component
                └── TaskCard[] - Client Component

Modals (Rendered by TaskBoard):
├── TaskFormDialog (Create) - Client Component
│   └── TaskForm - Client Component
└── TaskFormDialog (Edit) - Client Component
    └── TaskForm - Client Component
```

### 8.2 Component Specifications

**TaskBoard (Client Component):**
- Props: `{ initialTasks: Task[] }`
- State: Current tasks, active modal, drag state
- Responsibilities: Coordinate task operations, manage modals
- Calls: Server Actions for CRUD operations

**TaskColumn (Client Component):**
- Props: `{ status, tasks, onTaskUpdate, onTaskDelete }`
- Renders: Column header, task list, empty state
- For "To Do": Includes "Add Task" button

**TaskCard (Client Component):**
- Props: `{ task, onEdit, onDelete, onStatusChange }`
- Renders: Title, note preview, timestamp, action buttons
- Interactions: Click to edit, drag to move, dropdown for status

**TaskForm (Client Component):**
- Props: `{ mode: 'create' | 'edit', task?, onSubmit, onCancel }`
- Validation: Zod schema for title/note
- Calls: Server Actions (createTaskAction or updateTaskAction)

**CompletionSummary (Server Component or Client):**
- Props: `{ completedToday, completedThisWeek }`
- Renders: Completion counts
- Option: Make client component for real-time updates

### 8.3 Integration Points

**Database ↔ Server Components:**
- Direct Drizzle ORM queries in Server Components
- Type-safe, no API layer needed

**Server Actions ↔ Client Components:**
- Client calls Server Actions directly
- Type-safe with TypeScript
- Automatic serialization of FormData

**Client ↔ Client Communication:**
- Props and callbacks (parent → child)
- Lift state to TaskBoard (shared state)

---

## 9. Architecture Decision Records (ADRs)

### ADR-001: Use Next.js 15 App Router with Server Components

**Date:** 2025-10-21
**Status:** Accepted
**Decider:** Architect

**Context:**
Need to choose a React framework for Level 1 web application with local database persistence and simple CRUD operations.

**Options Considered:**

1. **Next.js 15 App Router (Server Components)**
   - Pros: Optimal performance (less client JS), Server Actions eliminate API boilerplate, excellent TypeScript support, modern React patterns
   - Cons: Learning curve for Server Components paradigm

2. **Next.js Pages Router**
   - Pros: More familiar, simpler mental model, well-documented
   - Cons: Requires API routes for backend, more boilerplate, larger client bundles

3. **Vite + React (SPA)**
   - Pros: Fast dev server, simple setup, full client-side control
   - Cons: Requires separate backend (Express/Fastify), more configuration, larger bundles

**Decision:**
Use Next.js 15 App Router with React Server Components

**Rationale:**
- Server Components reduce client JavaScript for faster loads (NFR2: < 300ms)
- Server Actions provide type-safe mutations without API route boilerplate
- Built-in optimizations (image, font) improve performance
- Excellent TypeScript integration (strict mode requirement NFR3)
- Future-proof (latest React patterns)
- Simpler architecture (no separate API layer needed)

**Consequences:**
- **Positive**: Faster page loads, less code to maintain, type-safe end-to-end
- **Negative**: Team must learn Server Component patterns
- **Neutral**: Some components marked `'use client'` (TaskBoard, forms)

---

### ADR-002: Use SQLite with Drizzle ORM

**Date:** 2025-10-21
**Status:** Accepted
**Decider:** Architect

**Context:**
Need local-only database persistence (NFR1: runs entirely locally) with ACID guarantees (NFR: zero data loss per US7).

**Options Considered:**

1. **SQLite + Drizzle ORM**
   - Pros: Lightweight, type-safe, minimal overhead, excellent DX, synchronous API
   - Cons: Newer ORM (less community resources than Prisma)

2. **SQLite + Prisma**
   - Pros: Mature ecosystem, excellent docs, widely adopted
   - Cons: Heavier runtime, slower startup, async-only API (unnecessary complexity for local DB)

3. **IndexedDB (browser storage)**
   - Pros: No server needed, built into browser
   - Cons: Requires client-side code, harder to query, not suitable for Next.js SSR

4. **JSON file storage**
   - Pros: Simple, human-readable
   - Cons: No ACID guarantees, risk of data corruption, poor query performance

**Decision:**
Use SQLite with Drizzle ORM

**Rationale:**
- SQLite: Local-only (meets NFR1), ACID compliance (meets US7), zero-config
- Drizzle: Type-safe queries, minimal runtime, excellent TypeScript DX, fast
- Synchronous API appropriate for local database (no network latency)
- Better-sqlite3 driver is mature and reliable
- Drizzle migration system is simple and effective

**Consequences:**
- **Positive**: Type-safe database access, fast queries, minimal dependencies
- **Negative**: Less community resources than Prisma (acceptable tradeoff)
- **Neutral**: Database file stored in `./data/tasks.db` (documented)

---

### ADR-003: Use shadcn/ui + Tailwind CSS for UI

**Date:** 2025-10-21
**Status:** Accepted
**Decider:** Architect

**Context:**
Need professional, accessible UI components that meet UX spec requirements (WCAG 2.1 Level A, keyboard navigation, Illustrate Inc. brand colors).

**Options Considered:**

1. **shadcn/ui + Tailwind CSS**
   - Pros: Own the code (full customization), built on Radix UI (accessibility), copy-paste (no bloat), excellent DX
   - Cons: Manual component installation (acceptable for small project)

2. **Material UI (MUI)**
   - Pros: Comprehensive component library, mature ecosystem
   - Cons: Heavier bundle, less customization, different design language than UX spec

3. **Custom components + Tailwind**
   - Pros: Full control, minimal dependencies
   - Cons: Must implement accessibility manually, slower development, higher risk of bugs

4. **Headless UI + Tailwind**
   - Pros: Unstyled primitives, good accessibility
   - Cons: Less comprehensive than shadcn/ui, more manual work

**Decision:**
Use shadcn/ui + Tailwind CSS

**Rationale:**
- shadcn/ui built on Radix UI (accessibility built-in: keyboard nav, ARIA, focus management)
- Copy-paste model: own the code, customize freely (Illustrate Inc. brand colors)
- Tailwind CSS: Rapid development, matches UX spec spacing/color system
- No NPM bloat: only install components you use
- Excellent TypeScript support (strict mode requirement)
- Fast implementation (meets project timeline)

**Consequences:**
- **Positive**: Accessible by default, rapid development, full customization, small bundle
- **Negative**: Must manually install each component (minor overhead)
- **Neutral**: Tailwind config customized for Illustrate brand colors (documented in UX spec)

---

### ADR-004: Use Server Actions instead of API Routes

**Date:** 2025-10-21
**Status:** Accepted
**Decider:** Architect

**Context:**
Need to handle task mutations (create, update, delete) from client components with type safety and minimal boilerplate.

**Options Considered:**

1. **Server Actions**
   - Pros: No API routes needed, type-safe, less code, integrated with Next.js
   - Cons: Newer pattern (less familiar)

2. **Next.js API Routes**
   - Pros: Traditional REST pattern, well-documented
   - Cons: More boilerplate, separate route files, manual type safety

3. **tRPC**
   - Pros: End-to-end type safety, excellent DX
   - Cons: Additional dependency, overkill for simple CRUD, learning curve

**Decision:**
Use Server Actions for all mutations

**Rationale:**
- Simpler than API routes (no separate files)
- Type-safe out of the box (TypeScript from client to database)
- Integrated with Next.js revalidation (`revalidatePath`)
- Reduces code volume (fewer files to maintain)
- Appropriate for Level 1 project (simple CRUD)

**Consequences:**
- **Positive**: Less code, type-safe, simpler architecture
- **Negative**: Team must learn Server Actions pattern (minimal learning curve)
- **Neutral**: Validation with Zod (would be needed anyway)

---

### ADR-005: Use Vitest + Playwright for Testing

**Date:** 2025-10-21
**Status:** Accepted
**Decider:** Architect

**Context:**
Need testing frameworks for NFR5 (>70% unit test coverage) and NFR6 (100% E2E coverage of user stories).

**Options Considered:**

1. **Vitest + Playwright**
   - Pros: Fast, modern, excellent TypeScript DX, Vite-powered
   - Cons: Newer than Jest (less community content)

2. **Jest + Cypress**
   - Pros: Mature, widely adopted, extensive resources
   - Cons: Jest slower, Cypress less reliable, more configuration

3. **Jest + Playwright**
   - Pros: Best of both (Jest maturity + Playwright reliability)
   - Cons: Jest slower than Vitest

**Decision:**
Use Vitest for unit tests, Playwright for E2E tests

**Rationale:**
- Vitest: Fast (Vite-powered), Jest-compatible API, excellent TypeScript support
- Playwright: More reliable than Cypress, better debugging, cross-browser support
- Both have excellent DX and documentation
- Appropriate for Next.js + TypeScript stack

**Consequences:**
- **Positive**: Fast tests, great DX, reliable E2E
- **Negative**: Less community content than Jest (acceptable)
- **Neutral**: Configuration straightforward

---

### ADR-006: No Drag-and-Drop in MVP (Dropdown for Status Changes)

**Date:** 2025-10-21
**Status:** Accepted
**Decider:** Architect

**Context:**
UX spec leaves task movement interaction pattern as open design decision (PRD Section 6.1). Options: drag-drop, dropdown, or buttons.

**Options Considered:**

1. **Dropdown selector**
   - Pros: Simple to implement, keyboard accessible, works on all devices
   - Cons: Less intuitive than drag-drop, one extra click

2. **Drag-and-drop (dnd-kit)**
   - Pros: Intuitive, satisfying interaction, matches Kanban pattern
   - Cons: More complex, accessibility challenges, mobile touch support needed

3. **Button-based (Move To...)**
   - Pros: Simplest implementation, fully accessible
   - Cons: More clicks, less intuitive

**Decision:**
Use dropdown selector for MVP, consider drag-drop in future iteration

**Rationale:**
- Simpler implementation (faster MVP delivery)
- Fully keyboard accessible (meets NFR: accessibility)
- Works on all viewports (desktop focus per PRD, but future-proof)
- Can add drag-drop later without breaking changes (progressive enhancement)
- Appropriate for Level 1 project scope

**Consequences:**
- **Positive**: Faster MVP, fully accessible, simpler testing
- **Negative**: Less intuitive than drag-drop (acceptable for MVP)
- **Neutral**: Future enhancement clearly documented

---

### ADR-007: Modal Dialog for Task Creation/Editing

**Date:** 2025-10-21
**Status:** Accepted
**Decider:** Architect

**Context:**
UX spec leaves form presentation as open decision: modal, slide-out panel, or inline editing.

**Options Considered:**

1. **Modal dialog (centered)**
   - Pros: Familiar pattern, focus isolation, works with shadcn/ui Dialog
   - Cons: Slight context switch from board

2. **Slide-out panel (side drawer)**
   - Pros: Maintains board visibility, modern pattern
   - Cons: More complex, less familiar, harder on smaller screens

3. **Inline editing**
   - Pros: Fastest for simple title changes
   - Cons: Harder for multi-field edits (title + note), more complex state management

**Decision:**
Use modal dialog for both create and edit operations

**Rationale:**
- Consistent pattern (same UI for create and edit)
- shadcn/ui Dialog component has built-in accessibility (focus trap, Esc to close)
- Familiar pattern for users
- Simpler implementation than slide-out or inline
- Appropriate for form with 2 fields (title + note)

**Consequences:**
- **Positive**: Consistent UX, accessible, simple implementation
- **Negative**: Slight context switch (acceptable)
- **Neutral**: Could add inline title editing later for quick changes

---

## 10. Implementation Guidance

### 10.1 Development Workflow

**Initial Setup:**
1. Initialize Next.js 15 project with TypeScript
2. Install dependencies (see Technology Stack table)
3. Configure Tailwind CSS with Illustrate brand colors
4. Initialize shadcn/ui CLI
5. Set up Drizzle ORM and database
6. Create database schema and run migrations

**Component Implementation Order:**
1. Database layer (schema, queries, actions)
2. Basic UI components (shadcn/ui: Button, Input, Textarea, Dialog, Card)
3. Server Components (Page, CompletionSummary)
4. Client Components (TaskCard → TaskColumn → TaskBoard)
5. TaskForm component
6. Wire up Server Actions
7. Add animations and polish
8. Accessibility review and testing
9. Unit and E2E tests

**Testing Strategy:**
1. Unit tests: Business logic, utilities, data access functions
2. Component tests: React Testing Library for component behavior
3. E2E tests: Playwright for full user flows (US1-US7)
4. Manual testing: Keyboard navigation, accessibility

### 10.2 Key Implementation Notes

**Server Actions Best Practices:**
- Always validate input with Zod
- Always call `revalidatePath('/')` after mutations
- Return `{ success: boolean; error?: string }` for error handling
- Use FormData for form submissions (progressive enhancement)

**Database Best Practices:**
- Use transactions for multi-step operations (not needed for simple CRUD)
- Index columns used in WHERE clauses (status, created_at, completed_at)
- Use prepared statements (Drizzle does this automatically)
- Back up database file regularly (future: add export feature)

**Performance Checklist:**
- Minimize 'use client' boundaries (keep most components as Server Components)
- Use React.lazy() for heavy components (if needed)
- Optimize images with Next.js Image component (if images added)
- Measure with Lighthouse (target: 90+ performance score)

**Accessibility Checklist:**
- Test with keyboard only (no mouse)
- Run axe DevTools scan (0 violations target)
- Test focus management in modals
- Verify ARIA labels with screen reader
- Check color contrast (WCAG AA)

### 10.3 Environment Setup

**Required Environment:**
- Node.js 20.x LTS
- npm 10.x or pnpm 8.x
- Modern IDE with TypeScript support (VS Code recommended)

**Development Scripts:**
```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "type-check": "tsc --noEmit",
    "test": "vitest",
    "test:e2e": "playwright test",
    "db:generate": "drizzle-kit generate",
    "db:migrate": "drizzle-kit migrate",
    "db:studio": "drizzle-kit studio"
  }
}
```

**Pre-commit Checks:**
1. TypeScript compilation (`npm run type-check`)
2. ESLint validation (`npm run lint`)
3. Unit tests (`npm test`)
4. Prettier formatting (optional: set up pre-commit hook)

---

## 11. Proposed Source Tree

```
personal_task_manager/
├── src/
│   ├── app/                        # Next.js App Router
│   │   ├── layout.tsx              # Root layout (Server Component)
│   │   ├── page.tsx                # Home page (Server Component)
│   │   ├── error.tsx               # Error boundary
│   │   ├── loading.tsx             # Loading UI (optional)
│   │   ├── actions.ts              # Server Actions (create, update, delete tasks)
│   │   └── globals.css             # Global styles (Tailwind imports)
│   │
│   ├── components/                 # React components
│   │   ├── ui/                     # shadcn/ui base components
│   │   │   ├── button.tsx          # Button primitive
│   │   │   ├── card.tsx            # Card primitive
│   │   │   ├── dialog.tsx          # Dialog/Modal primitive
│   │   │   ├── dropdown-menu.tsx   # Dropdown menu primitive
│   │   │   ├── input.tsx           # Input primitive
│   │   │   └── textarea.tsx        # Textarea primitive
│   │   │
│   │   ├── task-board/             # Application-specific components
│   │   │   ├── TaskBoard.tsx       # Main board container (Client)
│   │   │   ├── TaskColumn.tsx      # Column component (Client)
│   │   │   ├── TaskCard.tsx        # Task card component (Client)
│   │   │   ├── TaskForm.tsx        # Task create/edit form (Client)
│   │   │   └── CompletionSummary.tsx # Completion widget (Server or Client)
│   │   │
│   │   └── providers/              # Context providers (if needed)
│   │       └── ToastProvider.tsx   # Toast notifications (optional)
│   │
│   ├── db/                         # Database layer
│   │   ├── index.ts                # Database connection (Drizzle + better-sqlite3)
│   │   ├── schema.ts               # Drizzle schema definitions
│   │   ├── queries.ts              # Database query functions
│   │   └── seed.ts                 # Seed data for development (optional)
│   │
│   ├── lib/                        # Utilities and helpers
│   │   ├── utils.ts                # General utilities (cn() for className merging)
│   │   ├── validations.ts          # Zod validation schemas
│   │   └── date-utils.ts           # Date formatting utilities (date-fns wrappers)
│   │
│   └── types/                      # TypeScript type definitions
│       └── index.ts                # Shared types (re-export from schema)
│
├── data/                           # SQLite database file
│   └── tasks.db                    # SQLite database (created on first run)
│
├── drizzle/                        # Drizzle ORM migrations
│   └── 0000_initial.sql            # Initial schema migration
│
├── tests/                          # E2E tests (Playwright)
│   ├── task-management.spec.ts     # E2E tests for all user stories
│   └── fixtures/                   # Test fixtures and helpers
│       └── db.ts                   # Database setup for tests
│
├── public/                         # Static assets
│   ├── favicon.ico                 # Favicon
│   └── assets/                     # Images, fonts (if needed)
│
├── node_modules/                   # Dependencies (gitignored)
│
├── .next/                          # Next.js build output (gitignored)
│
├── docs/                           # Documentation
│   ├── PRD.md                      # Product Requirements Document
│   ├── ux-specification.md         # UX/UI Specification
│   ├── solution-architecture.md    # This document
│   ├── project-workflow-analysis.md # Project analysis
│   └── tech-spec-epic-*.md         # Tech specs per epic (to be generated)
│
├── .env.local                      # Environment variables (local dev)
├── .eslintrc.json                  # ESLint configuration
├── .gitignore                      # Git ignore rules
├── .prettierrc                     # Prettier configuration (optional)
├── drizzle.config.ts               # Drizzle configuration
├── next.config.js                  # Next.js configuration
├── package.json                    # Dependencies and scripts
├── playwright.config.ts            # Playwright configuration
├── postcss.config.js               # PostCSS configuration (for Tailwind)
├── tailwind.config.ts              # Tailwind configuration (Illustrate brand colors)
├── tsconfig.json                   # TypeScript configuration (strict mode)
├── vitest.config.ts                # Vitest configuration
└── README.md                       # Project README

# Total Files (approximate): ~50 files
# Total Lines of Code (estimated): ~2,500-3,000 LOC
```

### 11.1 Key Directory Explanations

**`src/app/`** - Next.js App Router (Server Components by default)
- `page.tsx`: Main board view, fetches tasks server-side
- `actions.ts`: Server Actions for mutations
- `layout.tsx`: Root layout with headers, metadata

**`src/components/ui/`** - shadcn/ui primitives
- Copy-pasted from shadcn/ui CLI
- Built on Radix UI (accessibility built-in)
- Customized with Illustrate brand colors

**`src/components/task-board/`** - Application components
- TaskBoard: Main container (Client Component)
- TaskColumn: Column layout (Client Component)
- TaskCard: Individual task display (Client Component)
- TaskForm: Create/edit form (Client Component)
- CompletionSummary: Stats widget (Server or Client)

**`src/db/`** - Database layer
- `index.ts`: Drizzle connection
- `schema.ts`: Table definitions (tasks)
- `queries.ts`: Reusable query functions
- `seed.ts`: Development data (optional)

**`src/lib/`** - Utilities
- `utils.ts`: General helpers (cn() for Tailwind)
- `validations.ts`: Zod schemas for forms
- `date-utils.ts`: Date formatting (date-fns)

**`data/`** - SQLite database file location
- Created on first run
- Backed up manually (or add export feature)

**`tests/`** - E2E tests with Playwright
- One spec per user story (US1-US7)
- Test fixtures for database setup

**`docs/`** - Project documentation
- PRD, UX spec, architecture, tech specs

---

## 12. DevOps and Deployment (Simple - Inline)

**Deployment Model:** Local Development Only (MVP)

**No Production Deployment Required:**
- Application runs entirely locally (per NFR1)
- No hosting, no CI/CD, no cloud services
- Database file stored on local disk

**Development Environment:**
1. Clone repository
2. Run `npm install`
3. Run `npm run db:generate && npm run db:migrate` (create database)
4. Run `npm run dev` (start Next.js dev server)
5. Open `http://localhost:3000`

**Build for Production-like Testing:**
```bash
npm run build    # Build optimized Next.js app
npm start        # Run production build locally
```

**Future Deployment Options (Out of Scope for MVP):**
- Vercel deployment (if cloud sync added)
- Electron app (desktop distribution)
- Docker container (portable local deployment)

**Environment Variables:**
```
# .env.local (for development)
DATABASE_PATH=./data/tasks.db
NODE_ENV=development
```

---

## 13. Security (Simple - Inline)

**Threat Model:** Low Risk (Single-user, local-only application)

**Security Considerations:**

1. **No Authentication Required**
   - Single-user local application (per NFR1)
   - No user accounts, no passwords

2. **Input Validation**
   - Zod validation on all user inputs (title, note)
   - SQL injection prevented by Drizzle ORM (parameterized queries)
   - XSS prevention by React (automatic escaping)

3. **Data Privacy**
   - All data stored locally on user's machine
   - No telemetry, no analytics, no external requests
   - User owns their data completely

4. **Database Security**
   - SQLite file permissions: user-only read/write
   - No encryption (not needed for local-only MVP)
   - Future: Add optional database encryption

5. **Dependency Security**
   - Regular `npm audit` checks
   - Keep dependencies updated
   - Use LTS versions of Node.js

**Security Checklist:**
- [x] Input validation with Zod
- [x] SQL injection prevention (Drizzle ORM)
- [x] XSS prevention (React automatic escaping)
- [ ] Database file permissions (document best practice)
- [ ] Regular dependency updates (future maintenance)

---

## 14. Testing Strategy

### 14.1 Testing Pyramid

```
       /\
      /  \      E2E Tests (Playwright)
     /    \     - 7 test suites (one per user story)
    /      \    - Full workflow coverage
   /--------\
  /          \   Component Tests (React Testing Library)
 /            \  - UI component behavior
/              \ - Form validation
/--------------\
                 Unit Tests (Vitest)
                 - Business logic (data access, utilities)
                 - Pure functions (date formatting, validation)
                 - Target: >70% coverage
```

### 14.2 Unit Tests (Vitest)

**Coverage Target:** >70% (NFR5)

**Test Focus:**
- Database query functions (`src/db/queries.ts`)
- Validation schemas (`src/lib/validations.ts`)
- Utility functions (`src/lib/utils.ts`, `src/lib/date-utils.ts`)
- Server Actions (mocked database)

**Example:**
```typescript
// src/db/queries.test.ts
import { describe, it, expect, beforeEach, afterEach } from 'vitest';
import { createTask, updateTask, deleteTask } from './queries';
import { setupTestDb, teardownTestDb } from '@/tests/fixtures/db';

describe('Task Queries', () => {
  beforeEach(() => setupTestDb());
  afterEach(() => teardownTestDb());

  it('creates a new task with valid input', async () => {
    const task = await createTask({ title: 'Test Task', note: 'Test note' });

    expect(task).toMatchObject({
      title: 'Test Task',
      note: 'Test note',
      status: 'todo',
    });
    expect(task.id).toBeDefined();
    expect(task.createdAt).toBeInstanceOf(Date);
  });

  it('throws error when title is empty', async () => {
    await expect(createTask({ title: '' })).rejects.toThrow();
  });
});
```

### 14.3 Component Tests (React Testing Library)

**Coverage Target:** All UI components

**Test Focus:**
- Component rendering with props
- User interactions (click, type, submit)
- Form validation messages
- Accessibility (ARIA labels, keyboard navigation)

**Example:**
```typescript
// src/components/task-board/TaskForm.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { describe, it, expect, vi } from 'vitest';
import { TaskForm } from './TaskForm';

describe('TaskForm', () => {
  it('shows validation error for empty title', async () => {
    const onSubmit = vi.fn();
    render(<TaskForm mode="create" onSubmit={onSubmit} />);

    const submitButton = screen.getByRole('button', { name: /add task/i });
    fireEvent.click(submitButton);

    expect(await screen.findByText(/title is required/i)).toBeInTheDocument();
    expect(onSubmit).not.toHaveBeenCalled();
  });

  it('calls onSubmit with valid data', async () => {
    const onSubmit = vi.fn();
    render(<TaskForm mode="create" onSubmit={onSubmit} />);

    fireEvent.change(screen.getByLabelText(/title/i), {
      target: { value: 'Test Task' },
    });
    fireEvent.click(screen.getByRole('button', { name: /add task/i }));

    expect(onSubmit).toHaveBeenCalledWith({
      title: 'Test Task',
      note: undefined,
    });
  });
});
```

### 14.4 E2E Tests (Playwright)

**Coverage Target:** 100% of user stories (NFR6)

**Test Suites (7 total, one per user story):**

1. **US1: Quick Task Capture**
   - Navigate to app
   - Click "Add Task"
   - Fill title and note
   - Submit form
   - Verify task appears in "To Do" column

2. **US2: Visual Workflow Organization**
   - Open app with existing tasks
   - Verify 3 columns visible
   - Verify tasks sorted (most recent first)
   - Verify task counts per column

3. **US3: Task Progression**
   - Create task in "To Do"
   - Move to "Doing" (dropdown)
   - Move to "Done" (dropdown)
   - Verify task in correct column

4. **US4: Task Detail Management**
   - Create task
   - Click to edit
   - Update title and note
   - Save and verify changes

5. **US5: Task Removal**
   - Create task
   - Click delete
   - Confirm deletion
   - Verify task removed

6. **US6: Progress Visibility**
   - Create and complete task
   - Verify "Today" count increments
   - Verify "This Week" count increments

7. **US7: Persistent Personal Workspace**
   - Create 3 tasks
   - Close browser
   - Reopen app
   - Verify all tasks persist with correct status

**Example:**
```typescript
// tests/task-management.spec.ts
import { test, expect } from '@playwright/test';

test.describe('US1: Quick Task Capture', () => {
  test('user can create a task in less than 5 seconds', async ({ page }) => {
    await page.goto('/');

    // Click "Add Task" button
    await page.click('button:has-text("Add Task")');

    // Fill form
    await page.fill('input[name="title"]', 'Review PR for auth module');
    await page.fill('textarea[name="note"]', 'Check for security vulnerabilities');

    // Submit
    await page.click('button:has-text("Add Task")');

    // Verify task appears in To Do column
    const todoColumn = page.locator('[aria-label="To Do tasks"]');
    await expect(todoColumn.locator('text=Review PR for auth module')).toBeVisible();
  });

  test('shows error when title is empty', async ({ page }) => {
    await page.goto('/');
    await page.click('button:has-text("Add Task")');

    // Submit without title
    await page.click('button[type="submit"]');

    // Verify error message
    await expect(page.locator('text=Title is required')).toBeVisible();
  });
});
```

### 14.5 Accessibility Testing

**Manual Tests:**
- [ ] Navigate entire app with keyboard only (Tab, Enter, Escape)
- [ ] Verify focus indicators visible on all interactive elements
- [ ] Test focus trap in modals
- [ ] Verify focus returns to trigger element after modal close
- [ ] Test with screen reader (VoiceOver or NVDA)

**Automated Tools:**
- [ ] Run axe DevTools browser extension (target: 0 violations)
- [ ] Run Lighthouse accessibility audit (target: 90+ score)
- [ ] Playwright accessibility assertions (built-in)

**Playwright Accessibility Example:**
```typescript
import { test, expect } from '@playwright/test';
import AxeBuilder from '@axe-core/playwright';

test('should not have accessibility violations', async ({ page }) => {
  await page.goto('/');

  const results = await new AxeBuilder({ page }).analyze();

  expect(results.violations).toEqual([]);
});
```

### 14.6 Performance Testing

**Metrics to Measure:**
- Page load time (target: < 300ms per NFR2)
- Task creation time (target: < 5s per US1)
- Lighthouse performance score (target: 90+)

**Tools:**
- Chrome DevTools Performance tab
- Lighthouse CI
- Web Vitals (CLS, FID, LCP)

**Playwright Performance Example:**
```typescript
test('page loads in under 300ms', async ({ page }) => {
  const start = Date.now();
  await page.goto('/');
  const loadTime = Date.now() - start;

  expect(loadTime).toBeLessThan(300);
});
```

---

## 15. Epic Alignment and Implementation Roadmap

### 15.1 Epic Breakdown

Based on the PRD user stories, we can organize implementation into logical epics:

**Epic 1: Foundation Setup**
- **Scope:** Project initialization, database setup, core infrastructure
- **Stories:** N/A (technical enabler)
- **Components:** Database schema, Drizzle ORM, Next.js project structure
- **Estimated Effort:** 0.5 days
- **Dependencies:** None
- **Deliverables:** Working Next.js app, database initialized, basic routing

**Epic 2: Task Display and Organization (US2, US7)**
- **Scope:** Display tasks in three-column layout with persistence
- **Stories:** US2 (Visual Workflow Organization), US7 (Persistent Workspace)
- **Components:** TaskBoard, TaskColumn, TaskCard (read-only), CompletionSummary
- **Data Models:** Task entity, getAllTasks query
- **APIs:** Server Component data fetching
- **Estimated Effort:** 1.5 days
- **Dependencies:** Epic 1
- **Deliverables:** Three-column board, tasks load from database, responsive layout

**Epic 3: Task Creation (US1)**
- **Scope:** Quick task capture with title and optional note
- **Stories:** US1 (Quick Task Capture)
- **Components:** TaskForm (create mode), AddTaskButton
- **Data Models:** Task creation validation
- **APIs:** createTaskAction (Server Action)
- **Estimated Effort:** 1 day
- **Dependencies:** Epic 2
- **Deliverables:** "Add Task" button, modal form, task creation < 5 seconds

**Epic 4: Task Management (US3, US4, US5)**
- **Scope:** Move, edit, and delete tasks
- **Stories:** US3 (Task Progression), US4 (Task Detail Management), US5 (Task Removal)
- **Components:** TaskCard (interactive), TaskForm (edit mode), DeleteConfirmation
- **Data Models:** Task update, status change, deletion
- **APIs:** updateTaskAction, updateTaskStatusAction, deleteTaskAction
- **Estimated Effort:** 2 days
- **Dependencies:** Epic 3
- **Deliverables:** Status dropdown, edit modal, delete confirmation, smooth animations

**Epic 5: Progress Tracking (US6)**
- **Scope:** Completion summary widget
- **Stories:** US6 (Progress Visibility)
- **Components:** CompletionSummary (enhanced)
- **Data Models:** Completion count queries
- **APIs:** getCompletionCounts query
- **Estimated Effort:** 0.5 days
- **Dependencies:** Epic 4
- **Deliverables:** Real-time completion counts, "Today" and "This Week" stats

**Epic 6: Polish and Accessibility**
- **Scope:** Animations, keyboard navigation, accessibility compliance
- **Stories:** All (US1-US7 accessibility requirements)
- **Components:** All components (add animations, ARIA labels, keyboard handlers)
- **Estimated Effort:** 1.5 days
- **Dependencies:** Epic 5
- **Deliverables:** Smooth animations, full keyboard nav, WCAG 2.1 Level A compliance

**Epic 7: Testing and Quality Assurance**
- **Scope:** Unit tests, E2E tests, performance optimization
- **Stories:** All (NFR5, NFR6 compliance)
- **Components:** Test suites for all components and user flows
- **Estimated Effort:** 2 days
- **Dependencies:** Epic 6
- **Deliverables:** >70% unit coverage, 100% E2E coverage, all tests passing

### 15.2 Implementation Timeline

**Total Estimated Effort:** 9 days (1.8 weeks for single developer)

```
Week 1:
  Day 1-2: Epic 1 (Foundation) + Epic 2 (Display)
  Day 3: Epic 3 (Creation)
  Day 4-5: Epic 4 (Management)

Week 2:
  Day 6: Epic 5 (Progress) + Epic 6 (Polish)
  Day 7-8: Epic 7 (Testing)
  Day 9: Buffer for fixes and documentation
```

**Critical Path:**
Epic 1 → Epic 2 → Epic 3 → Epic 4 → Epic 5 → Epic 6 → Epic 7

**Parallel Work Opportunities:**
- Epic 6 (accessibility) can start during Epic 4-5 (incremental)
- Epic 7 (testing) can start during Epic 3-4 (TDD approach)

### 15.3 Epic-to-Component Mapping

| Epic | Components | Data Models | Server Actions | Tests |
|------|------------|-------------|----------------|-------|
| Epic 1 | N/A | Task schema | N/A | N/A |
| Epic 2 | TaskBoard, TaskColumn, TaskCard, CompletionSummary | Task | N/A | Unit: queries |
| Epic 3 | TaskForm, AddTaskButton | Task validation | createTaskAction | E2E: US1 |
| Epic 4 | TaskCard (interactive), TaskForm (edit), DeleteConfirmation | Task update/delete | updateTaskAction, updateTaskStatusAction, deleteTaskAction | E2E: US3-US5 |
| Epic 5 | CompletionSummary (enhanced) | Completion counts | N/A | E2E: US6 |
| Epic 6 | All (animations, a11y) | N/A | N/A | Accessibility tests |
| Epic 7 | Test suites | N/A | N/A | All unit + E2E |

---

## 16. Next Steps and Recommendations

### 16.1 Immediate Next Steps

1. **Review and Approve Architecture**
   - Stakeholder review of this document
   - Confirm technology choices
   - Approve timeline and epic breakdown

2. **Generate Tech Specs per Epic**
   - Create detailed tech specs for each epic (Epic 1-7)
   - Define component APIs and contracts
   - Document data flows and state management

3. **Set Up Development Environment**
   - Initialize Next.js project
   - Install dependencies
   - Configure Tailwind, Drizzle, ESLint, Prettier
   - Set up Git repository

4. **Begin Implementation (Epic 1)**
   - Create database schema
   - Set up Drizzle migrations
   - Implement basic project structure

### 16.2 Risks and Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Server Components learning curve | Medium | Low | Allocate extra time for Epic 1-2, reference Next.js docs |
| Drizzle ORM unfamiliarity | Medium | Low | Start with simple queries, reference examples, use Drizzle Studio |
| Accessibility compliance gaps | Medium | Medium | Use shadcn/ui (built-in a11y), test early and often with axe |
| Timeline overrun (9 days → 12 days) | Low | Low | Buffer day included, scope well-defined, Level 1 project |
| Data loss bugs | Low | High | Thorough testing of persistence (US7), use SQLite transactions |

### 16.3 Future Enhancements (Post-MVP)

**Phase 2 (Nice-to-Have):**
- Drag-and-drop for task movement (replace dropdown)
- Inline editing for quick title changes
- Undo/redo functionality
- Task search and filtering
- Dark mode support

**Phase 3 (Advanced Features):**
- Task categories/tags
- Due dates and reminders
- Recurring tasks
- Export/import functionality (backup/restore)
- Multiple boards (work, personal)

**Phase 4 (Cloud Sync - Architectural Change):**
- User authentication (auth0 or Clerk)
- Cloud database (PostgreSQL on Vercel)
- Multi-device sync
- Collaborative features (share boards)

### 16.4 Success Metrics Validation

**Before Launch:**
- [ ] All functional requirements (FR1-FR10) implemented
- [ ] All user stories (US1-US7) deliverable
- [ ] Page load < 300ms (NFR2)
- [ ] Task creation < 5 seconds (US1)
- [ ] >70% unit test coverage (NFR5)
- [ ] 100% E2E test coverage (NFR6)
- [ ] TypeScript strict mode passes (NFR3)
- [ ] ESLint validation passes (NFR4)
- [ ] Accessibility: keyboard nav functional, axe DevTools 0 violations

**Post-Launch (User Validation):**
- [ ] Users complete full task workflow without assistance
- [ ] Zero data loss reports
- [ ] Positive feedback on speed and clarity
- [ ] Successful usage for 1+ week (dogfooding)

---

## 17. Conclusion

This solution architecture provides a complete, implementable design for Personal TaskBoard that meets all requirements specified in the PRD and UX specification. The architecture leverages modern Next.js patterns (Server Components, Server Actions) with a simple, type-safe stack (TypeScript, Drizzle ORM, SQLite) appropriate for a Level 1 greenfield web application.

**Key Architectural Strengths:**
- **Simple and focused:** Monolithic structure appropriate for scope
- **Type-safe end-to-end:** TypeScript from UI to database
- **Performance-optimized:** Server Components, minimal client JS
- **Accessible by default:** shadcn/ui built on Radix UI
- **Testable:** Clear separation of concerns, pure functions
- **Maintainable:** Well-documented, standard patterns

**Implementation Ready:**
- All components specified with clear responsibilities
- Technology choices documented with versions and rationale
- Data model defined with schema and indexes
- Testing strategy comprehensive (unit + E2E + accessibility)
- Epic breakdown provides clear implementation roadmap

**Next Steps:**
1. Generate tech specs for each epic (Epic 1-7)
2. Set up development environment
3. Begin Epic 1 (Foundation Setup)
4. Iterate through epics following implementation timeline

This architecture is ready for implementation and meets all acceptance criteria for the solution architecture phase of the BMAD workflow.

---

**Document Status:** Complete ✅
**Ready for:** Tech Spec Generation (Step 9 of workflow)
**Approved By:** Pending review
**Date:** 2025-10-21

---

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>
