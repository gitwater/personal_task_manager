# Implementation Guide: All Epics Tech Specs

**Project:** Personal TaskBoard
**Date:** 2025-10-21
**Total Epics:** 7
**Status:** Ready for Implementation

---

## Epic Implementation Summary

| Epic | Name | Stories | Effort | Status | Dependencies |
|------|------|---------|--------|--------|--------------|
| Epic 1 | Foundation Setup | N/A | 0.5 days | ✅ Spec Complete | None |
| Epic 2 | Task Display (US2, US7) | US2, US7 | 1.5 days | ✅ Spec Complete | Epic 1 |
| Epic 3 | Task Creation (US1) | US1 | 1 day | ✅ Spec Complete | Epic 2 |
| Epic 4 | Task Management (US3-US5) | US3, US4, US5 | 2 days | ✅ Spec Complete | Epic 3 |
| Epic 5 | Progress Tracking (US6) | US6 | 0.5 days | ✅ Spec Complete | Epic 4 |
| Epic 6 | Polish & Accessibility | All | 1.5 days | ✅ Integrated | Epic 5 |
| Epic 7 | Testing & QA | All | 2 days | ✅ Integrated | Epic 6 |

---

## Quick Start: Implementation Order

```bash
# Week 1
Day 1-2: Epic 1 + Epic 2
Day 3: Epic 3
Day 4-5: Epic 4

# Week 2
Day 6: Epic 5 + Epic 6
Day 7-8: Epic 7
Day 9: Buffer
```

---

## Epic 2: Task Display and Organization

### Components to Build

**1. TaskCard Component** (`components/task-board/TaskCard.tsx`)

```typescript
'use client';

import { Card, CardContent } from '@/components/ui/card';
import { Task } from '@/db/schema';
import { formatDistanceToNow } from 'date-fns';

interface TaskCardProps {
  task: Task;
}

export function TaskCard({ task }: TaskCardProps) {
  return (
    <Card className="mb-3 cursor-pointer transition-all hover:shadow-md hover:-translate-y-0.5">
      <CardContent className="p-4">
        <h3 className="font-medium text-neutral-800 mb-1">
          {task.title}
        </h3>
        {task.note && (
          <p className="text-sm text-neutral-600 mb-2 line-clamp-2">
            {task.note}
          </p>
        )}
        <p className="text-xs text-neutral-500">
          {formatDistanceToNow(task.createdAt, { addSuffix: true })}
        </p>
      </CardContent>
    </Card>
  );
}
```

**2. TaskColumn Component** (`components/task-board/TaskColumn.tsx`)

```typescript
'use client';

import { Task } from '@/db/schema';
import { TaskCard } from './TaskCard';

interface TaskColumnProps {
  title: string;
  status: 'todo' | 'doing' | 'done';
  tasks: Task[];
}

export function TaskColumn({ title, status, tasks }: TaskColumnProps) {
  return (
    <div className="bg-white rounded-xl p-5 min-h-[600px]">
      <h2 className="text-xl font-semibold text-neutral-800 mb-4 flex items-center justify-between">
        {title}
        <span className="text-sm font-normal text-neutral-500">
          {tasks.length}
        </span>
      </h2>

      <div className="space-y-3">
        {tasks.length === 0 ? (
          <EmptyState status={status} />
        ) : (
          tasks.map((task) => <TaskCard key={task.id} task={task} />)
        )}
      </div>
    </div>
  );
}

function EmptyState({ status }: { status: string }) {
  const messages = {
    todo: 'No tasks yet. Click "Add Task" to get started.',
    doing: 'Nothing in progress. Move a task here to start working.',
    done: 'No completed tasks yet. Keep going!',
  };

  return (
    <div className="text-center text-neutral-400 py-8">
      <p className="text-sm">{messages[status as keyof typeof messages]}</p>
    </div>
  );
}
```

**3. TaskBoard Component** (`components/task-board/TaskBoard.tsx`)

```typescript
'use client';

import { Task } from '@/db/schema';
import { TaskColumn } from './TaskColumn';

interface TaskBoardProps {
  initialTasks: Task[];
}

export function TaskBoard({ initialTasks }: TaskBoardProps) {
  const todoTasks = initialTasks.filter((t) => t.status === 'todo');
  const doingTasks = initialTasks.filter((t) => t.status === 'doing');
  const doneTasks = initialTasks.filter((t) => t.status === 'done');

  return (
    <div className="grid grid-cols-3 gap-6 max-w-7xl mx-auto px-6">
      <TaskColumn title="To Do" status="todo" tasks={todoTasks} />
      <TaskColumn title="Doing" status="doing" tasks={doingTasks} />
      <TaskColumn title="Done" status="done" tasks={doneTasks} />
    </div>
  );
}
```

**4. CompletionSummary Component** (`components/task-board/CompletionSummary.tsx`)

```typescript
interface CompletionSummaryProps {
  completedToday: number;
  completedThisWeek: number;
}

export function CompletionSummary({
  completedToday,
  completedThisWeek,
}: CompletionSummaryProps) {
  return (
    <div className="bg-white border-b border-neutral-200 py-4 px-6">
      <div className="max-w-7xl mx-auto flex items-center gap-6 text-sm">
        <span className="text-neutral-600">
          ✓ Completed Today:{' '}
          <strong className="text-neutral-800">{completedToday}</strong>
        </span>
        <span className="text-neutral-400">|</span>
        <span className="text-neutral-600">
          This Week:{' '}
          <strong className="text-neutral-800">{completedThisWeek}</strong>
        </span>
      </div>
    </div>
  );
}
```

**5. Database Queries** (`db/queries.ts`)

```typescript
import { db } from './index';
import { tasks, Task } from './schema';
import { eq, desc, gte, and } from 'drizzle-orm';
import { startOfDay, startOfWeek } from 'date-fns';

export async function getAllTasks(): Promise<Task[]> {
  return db.query.tasks.findMany({
    orderBy: [desc(tasks.createdAt)],
  });
}

export async function getCompletionCounts() {
  const now = new Date();
  const todayStart = startOfDay(now);
  const weekStart = startOfWeek(now, { weekStartsOn: 1 }); // Monday

  const allDoneTasks = await db.query.tasks.findMany({
    where: and(
      eq(tasks.status, 'done'),
      gte(tasks.completedAt, weekStart)
    ),
  });

  const completedToday = allDoneTasks.filter(
    (t) => t.completedAt! >= todayStart
  ).length;

  return {
    completedToday,
    completedThisWeek: allDoneTasks.length,
  };
}
```

**6. Update Page** (`app/page.tsx`)

```typescript
import { TaskBoard } from '@/components/task-board/TaskBoard';
import { CompletionSummary } from '@/components/task-board/CompletionSummary';
import { getAllTasks, getCompletionCounts } from '@/db/queries';

export default async function HomePage() {
  const [tasks, counts] = await Promise.all([
    getAllTasks(),
    getCompletionCounts(),
  ]);

  return (
    <>
      <CompletionSummary
        completedToday={counts.completedToday}
        completedThisWeek={counts.completedThisWeek}
      />
      <main className="py-8 bg-primary-100 min-h-screen">
        <TaskBoard initialTasks={tasks} />
      </main>
    </>
  );
}
```

---

## Epic 3: Task Creation (US1)

### Components to Build

**1. TaskForm Component** (`components/task-board/TaskForm.tsx`)

```typescript
'use client';

import { useState } from 'react';
import { Input } from '@/components/ui/input';
import { Textarea } from '@/components/ui/textarea';
import { Button } from '@/components/ui/button';
import { createTaskAction } from '@/app/actions';

interface TaskFormProps {
  onSuccess?: () => void;
}

export function TaskForm({ onSuccess }: TaskFormProps) {
  const [error, setError] = useState('');

  async function handleSubmit(formData: FormData) {
    const title = formData.get('title') as string;

    if (!title.trim()) {
      setError('Title is required');
      return;
    }

    const result = await createTaskAction(formData);

    if (result.success) {
      setError('');
      onSuccess?.();
    } else {
      setError(result.error || 'Failed to create task');
    }
  }

  return (
    <form action={handleSubmit} className="space-y-4">
      <div>
        <label htmlFor="title" className="block text-sm font-medium mb-1">
          Title *
        </label>
        <Input
          id="title"
          name="title"
          autoFocus
          maxLength={200}
          required
          placeholder="Enter task title..."
        />
        {error && (
          <p className="text-sm text-error mt-1">{error}</p>
        )}
      </div>

      <div>
        <label htmlFor="note" className="block text-sm font-medium mb-1">
          Note (optional)
        </label>
        <Textarea
          id="note"
          name="note"
          maxLength={2000}
          rows={4}
          placeholder="Add additional details..."
        />
      </div>

      <div className="flex justify-end gap-3">
        <Button type="button" variant="outline" onClick={onSuccess}>
          Cancel
        </Button>
        <Button type="submit">Add Task</Button>
      </div>
    </form>
  );
}
```

**2. Server Actions** (`app/actions.ts`)

```typescript
'use server';

import { revalidatePath } from 'next/cache';
import { z } from 'zod';
import { db } from '@/db';
import { tasks } from '@/db/schema';

const createTaskSchema = z.object({
  title: z.string().min(1, 'Title is required').max(200),
  note: z.string().max(2000).optional(),
});

export async function createTaskAction(formData: FormData) {
  try {
    const input = createTaskSchema.parse({
      title: formData.get('title'),
      note: formData.get('note') || undefined,
    });

    const now = new Date();

    await db.insert(tasks).values({
      id: crypto.randomUUID(),
      title: input.title.trim(),
      note: input.note?.trim(),
      status: 'todo',
      createdAt: now,
      updatedAt: now,
    });

    revalidatePath('/');

    return { success: true };
  } catch (error) {
    if (error instanceof z.ZodError) {
      return { success: false, error: error.errors[0].message };
    }
    return { success: false, error: 'Failed to create task' };
  }
}
```

---

## Epic 4: Task Management (US3, US4, US5)

### Additional Server Actions

```typescript
// app/actions.ts (continued)

export async function updateTaskAction(
  id: string,
  formData: FormData
) {
  try {
    const input = createTaskSchema.parse({
      title: formData.get('title'),
      note: formData.get('note') || undefined,
    });

    await db
      .update(tasks)
      .set({
        title: input.title.trim(),
        note: input.note?.trim(),
        updatedAt: new Date(),
      })
      .where(eq(tasks.id, id));

    revalidatePath('/');

    return { success: true };
  } catch (error) {
    return { success: false, error: 'Failed to update task' };
  }
}

export async function updateTaskStatusAction(
  id: string,
  newStatus: 'todo' | 'doing' | 'done'
) {
  try {
    const now = new Date();
    const updates: any = {
      status: newStatus,
      updatedAt: now,
    };

    if (newStatus === 'done') {
      updates.completedAt = now;
    } else {
      updates.completedAt = null;
    }

    await db
      .update(tasks)
      .set(updates)
      .where(eq(tasks.id, id));

    revalidatePath('/');

    return { success: true };
  } catch (error) {
    return { success: false, error: 'Failed to update task status' };
  }
}

export async function deleteTaskAction(id: string) {
  try {
    await db.delete(tasks).where(eq(tasks.id, id));

    revalidatePath('/');

    return { success: true };
  } catch (error) {
    return { success: false, error: 'Failed to delete task' };
  }
}
```

---

## Epic 5: Progress Tracking (US6)

Already implemented in Epic 2 with CompletionSummary component!

---

## Epic 6 & 7: Polish, Accessibility, and Testing

### Accessibility Checklist

```typescript
// Example: Update TaskCard for full accessibility

export function TaskCard({ task, onEdit, onDelete, onStatusChange }: TaskCardProps) {
  return (
    <Card
      className="mb-3 cursor-pointer transition-all hover:shadow-md hover:-translate-y-0.5"
      tabIndex={0}
      role="article"
      aria-label={`Task: ${task.title}`}
      onKeyDown={(e) => {
        if (e.key === 'Enter') onEdit(task.id);
        if (e.key === 'Delete') onDelete(task.id);
      }}
    >
      {/* ... */}
    </Card>
  );
}
```

### Testing Setup

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom',
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      threshold: {
        lines: 70,
        functions: 70,
        branches: 70,
        statements: 70,
      },
    },
  },
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './'),
    },
  },
});
```

---

## Installation Script

Save this as `scripts/setup.sh`:

```bash
#!/bin/bash

echo "📦 Installing dependencies..."
npm install

echo "🎨 Installing shadcn/ui components..."
npx shadcn@latest add button
npx shadcn@latest add card
npx shadcn@latest add dialog
npx shadcn@latest add dropdown-menu
npx shadcn@latest add input
npx shadcn@latest add textarea

echo "🗄️  Setting up database..."
mkdir -p data
npx drizzle-kit generate
npx drizzle-kit migrate

echo "✅ Setup complete!"
echo "Run 'npm run dev' to start the development server"
```

---

## Complete Package.json Scripts

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "type-check": "tsc --noEmit",
    "test": "vitest",
    "test:coverage": "vitest --coverage",
    "test:e2e": "playwright test",
    "db:generate": "drizzle-kit generate",
    "db:migrate": "drizzle-kit migrate",
    "db:studio": "drizzle-kit studio",
    "db:seed": "tsx db/seed.ts"
  }
}
```

---

## Quick Reference: Component Import Paths

```typescript
// UI Primitives (shadcn/ui)
import { Button } from '@/components/ui/button';
import { Card } from '@/components/ui/card';
import { Dialog } from '@/components/ui/dialog';
import { Input } from '@/components/ui/input';
import { Textarea } from '@/components/ui/textarea';

// App Components
import { TaskBoard } from '@/components/task-board/TaskBoard';
import { TaskColumn } from '@/components/task-board/TaskColumn';
import { TaskCard } from '@/components/task-board/TaskCard';
import { TaskForm } from '@/components/task-board/TaskForm';
import { CompletionSummary } from '@/components/task-board/CompletionSummary';

// Database
import { db } from '@/db';
import { tasks, type Task, type NewTask } from '@/db/schema';
import { getAllTasks, getCompletionCounts } from '@/db/queries';

// Server Actions
import { createTaskAction, updateTaskAction, deleteTaskAction } from '@/app/actions';
```

---

**Status:** ✅ All epics documented and ready for implementation

**Total Implementation Time:** ~9 days for single developer

**Next Step:** Begin Epic 1 (Foundation Setup)

---

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>
