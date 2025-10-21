# Epic 1: Foundation Setup

**Project:** Personal TaskBoard
**Epic ID:** EPIC-1
**Epic Owner:** Architect
**Status:** Ready for Implementation
**Estimated Effort:** 0.5 days
**Dependencies:** None
**Target Completion:** Day 1-2 (Week 1)

---

## Epic Overview

Establish the technical foundation for the Personal TaskBoard application by initializing the Next.js 15 project with TypeScript, configuring the core technology stack, setting up the database layer with SQLite and Drizzle ORM, and implementing the basic project structure. This epic creates the technical backbone that all subsequent features will build upon.

### Business Value

This epic enables all subsequent development by providing:
- Type-safe development environment with strict TypeScript configuration
- Local-only data persistence meeting NFR1 (runs entirely locally)
- Professional UI component foundation for rapid feature development
- Development workflow automation (linting, type-checking, database migrations)

### Technical Scope

- Next.js 15 App Router project initialization
- TypeScript strict mode configuration
- Tailwind CSS with Illustrate Inc. brand color customization
- SQLite database setup with Drizzle ORM
- shadcn/ui CLI initialization and base component installation
- Database schema definition and migration system
- Development tooling (ESLint, Prettier, testing framework setup)

---

## Success Criteria

- [ ] Next.js application runs successfully at `http://localhost:3000`
- [ ] TypeScript compilation passes with strict mode enabled (NFR3)
- [ ] ESLint validation passes with zero errors (NFR4)
- [ ] SQLite database file created at `./data/tasks.db`
- [ ] Database schema migrated successfully with `tasks` table
- [ ] shadcn/ui components installed and importable
- [ ] Tailwind CSS configured with Illustrate brand colors
- [ ] All npm scripts functional (`dev`, `build`, `lint`, `type-check`, `db:*`)
- [ ] Project structure matches proposed source tree from solution architecture

---

## Stories

### Story 1.1: Initialize Next.js 15 Project

**Description:** Create a new Next.js 15 project with TypeScript, App Router, and essential dependencies.

**Acceptance Criteria:**
- Next.js 15.0.3 installed with App Router enabled
- TypeScript 5.7.2 configured with strict mode
- React 19.0.0 installed
- Project runs successfully with `npm run dev`
- Basic `app/layout.tsx` and `app/page.tsx` created
- `.gitignore` configured to exclude `node_modules/`, `.next/`, and `data/`

**Technical Tasks:**
1. Run `npx create-next-app@latest` with options:
   - TypeScript: Yes
   - ESLint: Yes
   - Tailwind CSS: Yes
   - App Router: Yes
   - No src/ directory
2. Verify Node.js 20.x LTS is active
3. Install core dependencies:
   ```bash
   npm install zod date-fns lucide-react
   ```
4. Test development server starts successfully

**Estimated Effort:** 1 hour

---

### Story 1.2: Configure TypeScript and Linting

**Description:** Set up TypeScript strict mode and ESLint rules aligned with Next.js best practices and project requirements.

**Acceptance Criteria:**
- TypeScript strict mode enabled in `tsconfig.json`
- Path aliases configured (`@/` points to project root)
- ESLint configured with Next.js recommended rules
- Prettier installed and configured (optional but recommended)
- `npm run type-check` passes with zero errors
- `npm run lint` passes with zero errors

**Technical Tasks:**
1. Update `tsconfig.json`:
   ```json
   {
     "compilerOptions": {
       "strict": true,
       "noUncheckedIndexedAccess": true,
       "noImplicitOverride": true,
       "paths": {
         "@/*": ["./*"]
       }
     }
   }
   ```
2. Install ESLint dependencies:
   ```bash
   npm install -D @typescript-eslint/parser @typescript-eslint/eslint-plugin
   ```
3. Configure `.eslintrc.json` with Next.js and TypeScript rules
4. Add Prettier configuration (`.prettierrc`)
5. Add npm scripts:
   ```json
   {
     "type-check": "tsc --noEmit",
     "lint": "next lint",
     "format": "prettier --write ."
   }
   ```

**Estimated Effort:** 1 hour

---

### Story 1.3: Set Up Tailwind CSS with Brand Colors

**Description:** Configure Tailwind CSS with Illustrate Inc. brand colors and design system tokens from UX specification.

**Acceptance Criteria:**
- Tailwind CSS 3.4.17 configured
- Illustrate brand colors defined in `tailwind.config.ts`
- Design tokens for spacing, typography, and borders configured
- Global styles imported in `app/globals.css`
- Test page renders with brand colors successfully

**Technical Tasks:**
1. Update `tailwind.config.ts`:
   ```typescript
   colors: {
     primary: {
       100: '#E6F2FF',  // Illustrate Blue Light
       500: '#0066CC',  // Illustrate Blue Primary
       700: '#004C99',  // Illustrate Blue Dark
     },
     neutral: {
       100: '#F5F5F5',
       200: '#E0E0E0',
       400: '#999999',
       600: '#666666',
       800: '#333333',
     },
     success: '#4CAF50',
     warning: '#FF9800',
     error: '#F44336',
   }
   ```
2. Configure typography (Inter font family)
3. Set up spacing scale (4px base unit)
4. Import Tailwind directives in `app/globals.css`
5. Create test component to verify color system

**Estimated Effort:** 45 minutes

---

### Story 1.4: Install and Configure shadcn/ui

**Description:** Initialize shadcn/ui CLI and install essential UI primitive components.

**Acceptance Criteria:**
- shadcn/ui CLI initialized with Tailwind preset
- `components/ui/` directory created
- Core components installed: Button, Card, Dialog, Input, Textarea, Dropdown Menu
- Components import successfully from `@/components/ui/*`
- `lib/utils.ts` created with `cn()` helper function

**Technical Tasks:**
1. Initialize shadcn/ui:
   ```bash
   npx shadcn@latest init
   ```
2. Install core components:
   ```bash
   npx shadcn@latest add button
   npx shadcn@latest add card
   npx shadcn@latest add dialog
   npx shadcn@latest add dropdown-menu
   npx shadcn@latest add input
   npx shadcn@latest add textarea
   ```
3. Install Radix UI dependencies (automatically added by shadcn)
4. Install Lucide React icons:
   ```bash
   npm install lucide-react
   ```
5. Verify `lib/utils.ts` created with `cn()` function
6. Test component import in a demo page

**Estimated Effort:** 1 hour

---

### Story 1.5: Set Up SQLite Database with Drizzle ORM

**Description:** Configure SQLite database connection and Drizzle ORM for local data persistence.

**Acceptance Criteria:**
- SQLite database driver `better-sqlite3` installed
- Drizzle ORM 0.36.4 configured
- Drizzle Kit installed for migrations
- `db/index.ts` exports database connection
- `db/schema.ts` defines `tasks` table schema
- `data/` directory created for database file
- `drizzle.config.ts` configured
- Database connection verified with test query

**Technical Tasks:**
1. Install database dependencies:
   ```bash
   npm install drizzle-orm better-sqlite3
   npm install -D drizzle-kit @types/better-sqlite3
   ```
2. Create `db/index.ts`:
   ```typescript
   import Database from 'better-sqlite3';
   import { drizzle } from 'drizzle-orm/better-sqlite3';
   import * as schema from './schema';

   const sqlite = new Database('./data/tasks.db');
   export const db = drizzle(sqlite, { schema });
   ```
3. Create `db/schema.ts` with `tasks` table (see Story 1.6)
4. Create `drizzle.config.ts`:
   ```typescript
   import type { Config } from 'drizzle-kit';

   export default {
     schema: './db/schema.ts',
     out: './drizzle',
     driver: 'better-sqlite3',
     dbCredentials: {
       url: './data/tasks.db',
     },
   } satisfies Config;
   ```
5. Create `data/` directory:
   ```bash
   mkdir -p data
   ```
6. Add npm scripts:
   ```json
   {
     "db:generate": "drizzle-kit generate",
     "db:migrate": "drizzle-kit migrate",
     "db:studio": "drizzle-kit studio"
   }
   ```

**Estimated Effort:** 1.5 hours

---

### Story 1.6: Define Database Schema and Run Migrations

**Description:** Create the `tasks` table schema with all required fields, indexes, and constraints, then generate and run the initial migration.

**Acceptance Criteria:**
- `tasks` table schema defined with all fields from solution architecture
- Primary key, indexes, and CHECK constraint implemented
- Initial migration generated in `drizzle/` directory
- Migration applied successfully to database
- Database file created at `./data/tasks.db`
- TypeScript types inferred from schema (`Task`, `NewTask`)

**Technical Tasks:**
1. Create `db/schema.ts`:
   ```typescript
   import { sqliteTable, text, integer, index } from 'drizzle-orm/sqlite-core';

   export const tasks = sqliteTable('tasks', {
     id: text('id').primaryKey(),
     title: text('title').notNull(),
     note: text('note'),
     status: text('status', { enum: ['todo', 'doing', 'done'] })
       .notNull()
       .default('todo'),
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
2. Generate migration:
   ```bash
   npm run db:generate
   ```
3. Apply migration:
   ```bash
   npm run db:migrate
   ```
4. Verify database file created:
   ```bash
   ls -lh data/tasks.db
   ```
5. Test schema with Drizzle Studio:
   ```bash
   npm run db:studio
   ```

**Estimated Effort:** 1 hour

---

### Story 1.7: Create Basic Project Structure

**Description:** Set up the directory structure and placeholder files to match the proposed source tree from solution architecture.

**Acceptance Criteria:**
- All directories created: `components/`, `db/`, `lib/`, `types/`, `tests/`
- `components/task-board/` directory created for application components
- `lib/utils.ts`, `lib/validations.ts`, `lib/date-utils.ts` created with placeholder content
- `types/index.ts` created (re-exports from schema)
- Basic `app/layout.tsx` with metadata and root structure
- Placeholder `app/page.tsx` rendering "Personal TaskBoard"
- `docs/` directory contains existing PRD, UX spec, solution architecture

**Technical Tasks:**
1. Create directory structure:
   ```bash
   mkdir -p components/task-board
   mkdir -p lib
   mkdir -p types
   mkdir -p tests/fixtures
   mkdir -p drizzle
   mkdir -p public/assets
   ```
2. Create `lib/validations.ts`:
   ```typescript
   import { z } from 'zod';

   export const createTaskSchema = z.object({
     title: z.string().min(1, 'Title is required').max(200),
     note: z.string().max(2000).optional(),
   });

   export type CreateTaskInput = z.infer<typeof createTaskSchema>;
   ```
3. Create `lib/date-utils.ts`:
   ```typescript
   import { formatDistanceToNow, startOfDay, startOfWeek } from 'date-fns';

   export { formatDistanceToNow, startOfDay, startOfWeek };
   ```
4. Create `types/index.ts`:
   ```typescript
   export type { Task, NewTask } from '@/db/schema';
   ```
5. Update `app/layout.tsx` with proper metadata:
   ```typescript
   import type { Metadata } from 'next';
   import { Inter } from 'next/font/google';
   import './globals.css';

   const inter = Inter({ subsets: ['latin'] });

   export const metadata: Metadata = {
     title: 'Personal TaskBoard',
     description: 'Simple task management for knowledge workers',
   };

   export default function RootLayout({
     children,
   }: {
     children: React.ReactNode;
   }) {
     return (
       <html lang="en">
         <body className={inter.className}>{children}</body>
       </html>
     );
   }
   ```
6. Update `app/page.tsx`:
   ```typescript
   export default function HomePage() {
     return (
       <main className="min-h-screen bg-primary-100 flex items-center justify-center">
         <h1 className="text-4xl font-bold text-neutral-800">
           Personal TaskBoard
         </h1>
       </main>
     );
   }
   ```

**Estimated Effort:** 1 hour

---

### Story 1.8: Set Up Testing Framework

**Description:** Install and configure Vitest for unit tests and Playwright for E2E tests.

**Acceptance Criteria:**
- Vitest 3.1.4 installed and configured
- React Testing Library installed
- Playwright 1.49.1 installed
- `vitest.config.ts` configured with path aliases and jsdom environment
- `playwright.config.ts` configured
- Test scripts added to `package.json`
- Example unit test passes
- Example E2E test passes

**Technical Tasks:**
1. Install Vitest and Testing Library:
   ```bash
   npm install -D vitest @vitest/ui @testing-library/react @testing-library/jest-dom jsdom
   ```
2. Create `vitest.config.ts`:
   ```typescript
   import { defineConfig } from 'vitest/config';
   import react from '@vitejs/plugin-react';
   import path from 'path';

   export default defineConfig({
     plugins: [react()],
     test: {
       environment: 'jsdom',
       setupFiles: ['./tests/setup.ts'],
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
3. Create `tests/setup.ts`:
   ```typescript
   import '@testing-library/jest-dom';
   ```
4. Install Playwright:
   ```bash
   npm init playwright@latest
   ```
5. Add test scripts to `package.json`:
   ```json
   {
     "test": "vitest",
     "test:ui": "vitest --ui",
     "test:coverage": "vitest --coverage",
     "test:e2e": "playwright test",
     "test:e2e:ui": "playwright test --ui"
   }
   ```
6. Create example unit test: `lib/utils.test.ts`
7. Create example E2E test: `tests/homepage.spec.ts`
8. Run tests to verify setup:
   ```bash
   npm test -- --run
   npm run test:e2e
   ```

**Estimated Effort:** 1.5 hours

---

## Technical Architecture

### Directory Structure After Epic 1

```
personal_task_manager/
├── app/
│   ├── layout.tsx          # Root layout with Inter font
│   ├── page.tsx            # Placeholder home page
│   ├── globals.css         # Tailwind imports + custom styles
│   └── actions.ts          # (Created in Epic 3)
├── components/
│   ├── ui/                 # shadcn/ui components
│   │   ├── button.tsx
│   │   ├── card.tsx
│   │   ├── dialog.tsx
│   │   ├── dropdown-menu.tsx
│   │   ├── input.tsx
│   │   └── textarea.tsx
│   └── task-board/         # (Populated in Epic 2+)
├── db/
│   ├── index.ts            # Database connection
│   ├── schema.ts           # Drizzle schema (tasks table)
│   └── queries.ts          # (Created in Epic 2)
├── lib/
│   ├── utils.ts            # cn() and general utilities
│   ├── validations.ts      # Zod schemas
│   └── date-utils.ts       # date-fns wrappers
├── types/
│   └── index.ts            # Type re-exports
├── tests/
│   ├── setup.ts            # Test configuration
│   ├── fixtures/           # Test helpers
│   └── homepage.spec.ts    # Example E2E test
├── data/
│   └── tasks.db            # SQLite database (created on first run)
├── drizzle/
│   └── 0000_initial.sql    # Initial migration
├── docs/
│   ├── PRD.md
│   ├── ux-specification.md
│   ├── solution-architecture.md
│   ├── tech-spec-implementation-guide.md
│   └── epics/
│       └── epic-1.md       # This document
├── public/
│   └── assets/
├── .env.local              # Environment variables (optional)
├── .eslintrc.json          # ESLint configuration
├── .gitignore              # Git ignore rules
├── .prettierrc             # Prettier configuration
├── drizzle.config.ts       # Drizzle configuration
├── next.config.js          # Next.js configuration
├── package.json            # Dependencies and scripts
├── playwright.config.ts    # Playwright configuration
├── postcss.config.js       # PostCSS (for Tailwind)
├── tailwind.config.ts      # Tailwind + brand colors
├── tsconfig.json           # TypeScript strict mode
├── vitest.config.ts        # Vitest configuration
└── README.md               # Project README
```

---

## Dependencies and Packages

### Production Dependencies
```json
{
  "next": "15.0.3",
  "react": "19.0.0",
  "react-dom": "19.0.0",
  "typescript": "5.7.2",
  "drizzle-orm": "0.36.4",
  "better-sqlite3": "11.5.0",
  "zod": "3.24.1",
  "date-fns": "4.1.0",
  "lucide-react": "0.468.0",
  "@radix-ui/react-dialog": "^1.1.2",
  "@radix-ui/react-dropdown-menu": "^2.1.2",
  "@radix-ui/react-slot": "^1.1.0",
  "class-variance-authority": "^0.7.0",
  "clsx": "^2.1.1",
  "tailwind-merge": "^2.5.4"
}
```

### Development Dependencies
```json
{
  "@types/node": "^20.17.9",
  "@types/react": "^18.3.12",
  "@types/react-dom": "^18.3.1",
  "@types/better-sqlite3": "^7.6.12",
  "drizzle-kit": "0.30.0",
  "eslint": "9.17.0",
  "eslint-config-next": "15.0.3",
  "@typescript-eslint/parser": "8.18.2",
  "@typescript-eslint/eslint-plugin": "8.18.2",
  "prettier": "3.4.2",
  "tailwindcss": "3.4.17",
  "postcss": "^8.4.49",
  "autoprefixer": "^10.4.20",
  "vitest": "3.1.4",
  "@vitest/ui": "3.1.4",
  "@testing-library/react": "16.1.0",
  "@testing-library/jest-dom": "^6.6.3",
  "jsdom": "^25.0.1",
  "@vitejs/plugin-react": "^4.3.4",
  "@playwright/test": "1.49.1"
}
```

---

## Validation Checklist

Before marking Epic 1 as complete, verify:

### Development Environment
- [ ] Node.js 20.x LTS active (`node --version`)
- [ ] npm 10.x installed (`npm --version`)
- [ ] Git repository initialized

### Application Runtime
- [ ] `npm run dev` starts without errors
- [ ] Application loads at `http://localhost:3000`
- [ ] "Personal TaskBoard" heading visible on home page
- [ ] Tailwind brand colors applied (blue background)

### TypeScript Configuration
- [ ] `npm run type-check` passes with zero errors
- [ ] Strict mode enabled in `tsconfig.json`
- [ ] Path aliases working (`@/` imports resolve)

### Linting
- [ ] `npm run lint` passes with zero errors
- [ ] ESLint configured with Next.js rules
- [ ] Prettier configured (if using)

### Database
- [ ] `./data/tasks.db` file exists
- [ ] `npm run db:studio` opens Drizzle Studio
- [ ] `tasks` table visible in studio with correct schema
- [ ] All indexes created (status, created_at, completed_at)

### UI Components
- [ ] shadcn/ui components installed
- [ ] Can import Button from `@/components/ui/button`
- [ ] Can import Card from `@/components/ui/card`
- [ ] All 6 components importable without errors

### Testing
- [ ] `npm test -- --run` executes unit tests
- [ ] `npm run test:e2e` executes Playwright tests
- [ ] Example tests pass
- [ ] Coverage thresholds configured (70%)

### Project Structure
- [ ] All directories match proposed structure
- [ ] `lib/utils.ts`, `lib/validations.ts`, `lib/date-utils.ts` exist
- [ ] `components/task-board/` directory created
- [ ] `types/index.ts` exports Task types

### Build Process
- [ ] `npm run build` completes successfully
- [ ] `npm start` runs production build
- [ ] No build errors or warnings

---

## Risk Assessment

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| Node.js version mismatch | Low | Medium | Document required version in README, use `.nvmrc` file |
| Database file permissions | Low | High | Document database file location, add to .gitignore |
| shadcn/ui installation issues | Low | Low | Use exact component names, verify Radix UI peer dependencies |
| TypeScript strict mode errors | Medium | Low | Fix incrementally, use `@ts-expect-error` comments if needed temporarily |
| Drizzle ORM learning curve | Medium | Low | Reference official docs, use Drizzle Studio for debugging |

---

## Blockers and Dependencies

### External Dependencies
- None (all packages publicly available via npm)

### Internal Dependencies
- None (this is the first epic)

### Potential Blockers
- TypeScript strict mode may require additional type annotations
- better-sqlite3 native module may require compilation (node-gyp)
- Tailwind CSS intellisense may need VS Code extension

---

## Definition of Done

Epic 1 is complete when:

1. ✅ All 8 stories completed and acceptance criteria met
2. ✅ All validation checklist items checked
3. ✅ `npm run dev` starts successfully and application loads
4. ✅ `npm run type-check` passes with zero errors
5. ✅ `npm run lint` passes with zero errors
6. ✅ `npm run build` completes successfully
7. ✅ Database schema migrated and `tasks` table created
8. ✅ Example unit test and E2E test pass
9. ✅ All dependencies installed with correct versions
10. ✅ README.md updated with setup instructions
11. ✅ Code committed to Git with clear commit message
12. ✅ Epic 2 can begin immediately (no blockers)

---

## Next Steps

After completing Epic 1, proceed to:

**Epic 2: Task Display and Organization (US2, US7)**
- Build TaskBoard, TaskColumn, TaskCard components
- Implement database queries (getAllTasks, getCompletionCounts)
- Display tasks in three-column layout
- Verify data persistence across restarts

---

## Notes and Learnings

### Technical Decisions Made

1. **better-sqlite3 over node-sqlite3**: Synchronous API is simpler for local-only app
2. **Drizzle over Prisma**: Lighter weight, faster startup time, excellent TypeScript DX
3. **shadcn/ui over component libraries**: Own the code, full customization, no bloat
4. **Vitest over Jest**: Faster, modern, better TypeScript support

### Common Pitfalls to Avoid

- Don't skip TypeScript strict mode setup (harder to enable later)
- Don't forget to add `data/` directory to `.gitignore`
- Don't manually edit generated migrations (use Drizzle Kit)
- Don't import Server Components in Client Components (breaks hydration)

### Useful Commands Reference

```bash
# Development
npm run dev                 # Start dev server
npm run build              # Build for production
npm start                  # Run production build

# Type checking and linting
npm run type-check         # TypeScript validation
npm run lint               # ESLint validation
npm run format             # Prettier formatting (if configured)

# Database
npm run db:generate        # Generate migration from schema
npm run db:migrate         # Apply migrations
npm run db:studio          # Open Drizzle Studio

# Testing
npm test                   # Run unit tests (watch mode)
npm test -- --run          # Run unit tests once
npm run test:coverage      # Run with coverage report
npm run test:e2e           # Run Playwright E2E tests
npm run test:e2e:ui        # Run Playwright with UI
```

---

**Epic Status:** ✅ Ready for Implementation
**Next Epic:** Epic 2 - Task Display and Organization
**Estimated Start Date:** Day 1, Week 1
**Estimated Completion:** Day 2, Week 1

---

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>
