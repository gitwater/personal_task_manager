# Technical Specification: Foundation Setup

Date: 2025-10-21
Author: Kevin
Epic ID: 1-foundation
Status: Draft

---

## Overview

Epic 1: Foundation Setup establishes the core technical infrastructure for the Personal TaskBoard application. This epic creates the foundational codebase, configures the technology stack (Next.js 15, TypeScript, SQLite, Drizzle ORM, Tailwind CSS, shadcn/ui), initializes the database schema, and validates that all build tools and quality gates are operational. This is a technical enabler epic with no direct user-facing features, but it enables all subsequent epic implementations by providing a stable, type-safe, and well-configured development environment.

## Objectives and Scope

### In Scope
- Initialize Next.js 15 project with App Router and TypeScript strict mode
- Configure Tailwind CSS with Illustrate Inc. brand colors from UX specification
- Install and configure shadcn/ui component library with initial base components
- Set up SQLite database with better-sqlite3 driver
- Configure Drizzle ORM and define tasks table schema
- Generate and apply initial database migration
- Configure ESLint and TypeScript strict mode validation
- Initialize Git repository with proper .gitignore
- Verify all quality gates pass (TypeScript compilation, ESLint, dev server startup)

### Out of Scope
- Any user-facing UI components (covered in Epic 2+)
- Business logic implementation (covered in Epic 3+)
- Testing framework setup (deferred to Epic 7)
- Deployment configuration (out of scope for MVP)

### Success Criteria
- Development server runs without errors at http://localhost:3000
- TypeScript compilation passes with strict mode enabled (0 errors)
- ESLint validation passes (0 errors/warnings)
- SQLite database file exists at ./data/tasks.db with tasks table schema
- Drizzle Studio can connect and display tasks table
- All 6 initial shadcn/ui components installed and importable

## System Architecture Alignment

This epic directly implements the foundational decisions documented in the Solution Architecture:

- **ADR-001 (Next.js 15 App Router)**: Initializes Next.js 15 with App Router enabled, setting the foundation for Server Components pattern used throughout the application
- **ADR-002 (SQLite + Drizzle ORM)**: Establishes SQLite database with Drizzle ORM, providing type-safe database access layer
- **ADR-003 (shadcn/ui + Tailwind)**: Configures Tailwind CSS with Illustrate brand colors and installs shadcn/ui CLI for component management
- **Section 2.1 (Technology Stack)**: Implements exact versions specified in technology stack table (Next.js 15.0.3, TypeScript 5.7.2, Drizzle 0.36.4, etc.)
- **Section 5.1 (Database Schema)**: Creates tasks table with schema defined in architecture (id, title, note, status, timestamps, indexes)
- **Section 10.3 (Environment Setup)**: Establishes development scripts, TypeScript strict mode, ESLint configuration

**Alignment Validation:**
- Repository structure matches Section 3.1 (monorepo with app/, db/, components/ directories)
- Database location matches Section 5.3 (./data/tasks.db)
- TypeScript strict mode enabled per NFR3
- ESLint configured per NFR4

---

## Detailed Design

### Services and Modules

| Module | Responsibility | Inputs | Outputs | Owner/Location |
|--------|---------------|--------|---------|----------------|
| **Next.js App Router** | Application framework, routing, build system | User requests, React components | Rendered HTML, client bundles | `/app/` directory |
| **Drizzle ORM** | Database abstraction, query builder, migrations | Schema definitions | Type-safe database client, migrations | `/db/` directory |
| **shadcn/ui CLI** | Component library manager | Component requests | React components in `/components/ui/` | CLI tool + `/components/ui/` |
| **Tailwind CSS** | Styling framework | Tailwind classes in JSX | Compiled CSS bundle | `tailwind.config.ts` + `app/globals.css` |
| **ESLint** | Code quality validation | TypeScript/React files | Lint errors/warnings | `.eslintrc.json` |
| **TypeScript Compiler** | Type checking, compilation | `.ts`/`.tsx` files | Type errors, compiled JS (dev mode) | `tsconfig.json` |

**Module Interactions:**
1. Next.js imports components from `/components/ui/` (shadcn/ui)
2. Drizzle ORM connects to SQLite database file at `./data/tasks.db`
3. Tailwind processes CSS classes during Next.js build
4. ESLint validates all TypeScript files during `npm run lint`
5. TypeScript compiler runs during Next.js dev server and build

### Data Models and Contracts

#### Task Entity (Database Schema)

**Table:** `tasks`
**ORM:** Drizzle ORM
**Driver:** better-sqlite3

| Field | Type | Constraints | Purpose |
|-------|------|-------------|---------|
| `id` | TEXT | PRIMARY KEY | UUID v4, auto-generated |
| `title` | TEXT | NOT NULL | Task title, 1-200 chars |
| `note` | TEXT | NULLABLE | Optional note, 0-2000 chars |
| `status` | TEXT | NOT NULL, DEFAULT 'todo', CHECK IN ('todo', 'doing', 'done') | Task status |
| `created_at` | INTEGER | NOT NULL | Unix timestamp (milliseconds) |
| `updated_at` | INTEGER | NOT NULL | Unix timestamp (milliseconds) |
| `completed_at` | INTEGER | NULLABLE | Unix timestamp when moved to 'done' |

**Indexes:**
- `idx_tasks_status` ON `status` (for filtering by column)
- `idx_tasks_created_at` ON `created_at` DESC (for sorting)
- `idx_tasks_completed_at` ON `completed_at` DESC WHERE `completed_at` IS NOT NULL (for completion queries)

**TypeScript Types (Inferred from Schema):**

```typescript
// db/schema.ts
export type Task = typeof tasks.$inferSelect;
// Inferred as: { id: string; title: string; note: string | null; status: 'todo' | 'doing' | 'done'; createdAt: Date; updatedAt: Date; completedAt: Date | null; }

export type NewTask = typeof tasks.$inferInsert;
// Inferred as: { id: string; title: string; note?: string; status?: 'todo' | 'doing' | 'done'; createdAt: Date; updatedAt: Date; completedAt?: Date; }
```

**Schema Definition Location:** `db/schema.ts` (see implementation details in Workflows section below)

#### Configuration Files

**TypeScript Configuration** (`tsconfig.json`):
- Strict mode enabled (NFR3 requirement)
- Path alias `@/*` configured for clean imports
- Next.js plugin enabled for Server Components support

**Tailwind Configuration** (`tailwind.config.ts`):
- Illustrate Inc. brand colors configured (primary: #007acb, accent: #ffb81c)
- Custom border radius values (lg: 12px, md: 8px, sm: 6px)
- Dark mode support with class strategy
- tailwindcss-animate plugin for transitions

**shadcn/ui Configuration** (`components.json`):
- Base components installed: button, card, dialog, dropdown-menu, input, textarea
- Path aliases: `@/components`, `@/lib/utils`
- RSC (React Server Components) mode enabled

**Drizzle Configuration** (`drizzle.config.ts`):
- Schema path: `./db/schema.ts`
- Migrations output: `./drizzle/`
- Database location: `./data/tasks.db`

*(See Appendix A for complete configuration file contents)*

### APIs and Interfaces

Epic 1 establishes foundation only; no APIs or Server Actions created yet.

**Prepared for Future Use:**
- Database connection module at `db/index.ts` exports `db` client for query execution
- Type exports from `db/schema.ts` provide `Task` and `NewTask` types for Server Actions (Epic 3+)

**Database Client Interface:**

```typescript
// db/index.ts
import Database from 'better-sqlite3';
import { drizzle } from 'drizzle-orm/better-sqlite3';
import * as schema from './schema';

const sqlite = new Database('./data/tasks.db');
export const db = drizzle(sqlite, { schema });
// Usage in future epics: db.query.tasks.findMany(), db.insert(tasks).values(...), etc.
```

### Workflows and Sequencing

**Workflow: Initial Project Setup**

```
1. Initialize Next.js Project
   → Run: npx create-next-app@latest personal-task-manager --typescript --tailwind --app
   → Creates: app/, public/, package.json, tsconfig.json, tailwind.config.ts
   → Duration: ~2 minutes

2. Configure TypeScript Strict Mode
   → Edit: tsconfig.json (set "strict": true)
   → Validate: npx tsc --noEmit
   → Expected: 0 errors

3. Configure Tailwind with Illustrate Brand Colors
   → Edit: tailwind.config.ts (add primary, accent, neutral colors)
   → Test: Add temporary button with bg-primary-500 class
   → Verify: Button displays Illustrate blue (#007acb)

4. Install shadcn/ui
   → Run: npx shadcn@latest init
   → Select: Default style, Neutral base, CSS variables
   → Run: npx shadcn@latest add button card dialog dropdown-menu input textarea
   → Creates: components/ui/, lib/utils.ts, components.json

5. Install Database Dependencies
   → Run: npm install drizzle-orm better-sqlite3
   → Run: npm install -D drizzle-kit @types/better-sqlite3
   → Verify: package.json lists dependencies

6. Create Database Configuration
   → Create: drizzle.config.ts
   → Create: db/schema.ts (tasks table definition)
   → Create: db/index.ts (database connection)
   → Create: data/ directory

7. Generate and Apply Migration
   → Run: npx drizzle-kit generate
   → Creates: drizzle/0000_initial.sql
   → Run: npx drizzle-kit migrate
   → Creates: data/tasks.db with tasks table

8. Validate Setup
   → Run: npm run dev → Server starts on :3000
   → Run: npx tsc --noEmit → 0 errors
   → Run: npm run lint → 0 warnings/errors
   → Run: npx drizzle-kit studio → Database visible at :4983

9. Initialize Git
   → Run: git init
   → Create: .gitignore (exclude node_modules, .next, data/*.db)
   → Run: git add . && git commit -m "Initial commit: Foundation setup"
```

**Total Estimated Time:** 0.5 days (~4 hours including validation and testing)

---

## Non-Functional Requirements

### Performance

**Build Performance:**
- Next.js dev server cold start: < 10 seconds (target)
- TypeScript type-checking: < 5 seconds for initial project
- Page load (dev mode): < 300ms for default Next.js page

**Database Performance:**
- Migration execution: < 1 second
- Database file size after initialization: < 100 KB (empty tables)

**Rationale:** Foundation setup does not directly impact runtime performance but establishes baseline for future development. Next.js 15's Turbopack-based dev server provides fast refresh during development.

### Security

**Dependency Security:**
- All dependencies installed from npm registry with package-lock.json integrity checks
- No known vulnerabilities in installed packages (`npm audit` should report 0 vulnerabilities)

**Database Security:**
- SQLite database file created with user-only read/write permissions
- No sensitive data stored during Epic 1 (foundation only)

**Configuration Security:**
- No API keys or secrets required for Epic 1
- .gitignore configured to exclude sensitive files (.env*.local, database files)

### Reliability/Availability

**Build Reliability:**
- TypeScript strict mode catches type errors at compile time
- ES Lint configuration prevents common code quality issues
- Database migrations are idempotent (can be re-run safely)

**Error Handling:**
- Next.js dev server provides clear error messages for configuration issues
- Drizzle migrations fail fast with descriptive errors if schema conflicts exist

**Recovery:**
- Database can be reset by deleting `data/tasks.db` and re-running migrations
- Project can be re-initialized from scratch in < 30 minutes

### Observability

**Development Observability:**
- Next.js dev server logs all requests and compilation status to console
- TypeScript compiler provides detailed error locations and messages
- Drizzle Studio provides visual database inspection at http://localhost:4983

**Validation Checkpoints:**
- `npm run dev` → Confirms Next.js operational
- `npx tsc --noEmit` → Confirms TypeScript configuration valid
- `npm run lint` → Confirms ESLint configuration valid
- `npx drizzle-kit studio` → Confirms database schema correct

---

## Dependencies and Integrations

### External Dependencies

| Dependency | Version | Purpose | Source |
|------------|---------|---------|--------|
| next | 15.0.3 | Application framework | npm |
| react | 19.0.0 | UI library | npm (installed with Next.js) |
| react-dom | 19.0.0 | React renderer | npm (installed with Next.js) |
| typescript | 5.7.2 | Type system | npm |
| drizzle-orm | 0.36.4 | ORM for SQLite | npm |
| better-sqlite3 | 11.5.0 | SQLite driver | npm |
| drizzle-kit | 0.30.1 | Migration tool | npm (dev) |
| tailwindcss | 3.4.17 | CSS framework | npm (dev) |
| tailwindcss-animate | 1.0.7 | Tailwind animations | npm (dev) |
| eslint | 9.17.0 | Linting | npm (dev) |
| @radix-ui/* | ~1.1.2 | UI primitives (via shadcn/ui) | npm |
| lucide-react | 0.468.0 | Icons (via shadcn/ui) | npm |

**Dependency Installation:**
```bash
npm install drizzle-orm better-sqlite3
npm install -D drizzle-kit @types/better-sqlite3 tailwindcss-animate
# shadcn/ui components install their own dependencies (@radix-ui/*, etc.)
```

**Version Constraints:**
- Next.js 15 requires Node.js >= 18.17.0 (using Node 20.x LTS)
- better-sqlite3 requires compilation on installation (platform-specific)
- All versions match Solution Architecture Section 2.1 specification

### Integration Points

**File System:**
- Database file: `./data/tasks.db` (created on first migration)
- Migrations: `./drizzle/*.sql` (generated by drizzle-kit)
- Static assets: `./public/*` (served by Next.js)

**Build Tools:**
- Next.js build system integrates with TypeScript compiler
- Tailwind CLI processes CSS during Next.js build
- ESLint runs on `npm run lint` command

**No External Services:**
- All dependencies resolved from npm registry
- No API calls, no cloud services, no external databases
- Fully local development environment

---

## Acceptance Criteria (Authoritative)

**AC1: Next.js Project Initialized**
- ✅ Next.js 15.0.3 project created with App Router
- ✅ TypeScript strict mode enabled in `tsconfig.json`
- ✅ Development server runs without errors on http://localhost:3000
- ✅ Default Next.js page loads and displays correctly

**AC2: TypeScript Configuration Valid**
- ✅ `npx tsc --noEmit` completes with 0 errors
- ✅ Path alias `@/*` configured and functional
- ✅ Strict mode enabled (`"strict": true`)

**AC3: Tailwind CSS Configured**
- ✅ `tailwind.config.ts` contains Illustrate brand colors
- ✅ Primary color `primary-500` resolves to #007acb
- ✅ Test button with `bg-primary-500` displays correct blue color

**AC4: shadcn/ui Installed**
- ✅ `components.json` created with correct configuration
- ✅ 6 base components installed: button, card, dialog, dropdown-menu, input, textarea
- ✅ `lib/utils.ts` contains `cn()` helper function
- ✅ All components importable without errors

**AC5: Database Setup Complete**
- ✅ SQLite database file exists at `./data/tasks.db`
- ✅ `tasks` table created with correct schema (7 columns, 3 indexes)
- ✅ Drizzle Studio connects and displays tasks table schema
- ✅ `db/schema.ts` exports `Task` and `NewTask` types

**AC6: Migrations Applied**
- ✅ Migration file `drizzle/0000_initial.sql` generated
- ✅ Migration applied successfully (table exists in database)
- ✅ `npx drizzle-kit migrate` completes without errors

**AC7: Quality Gates Pass**
- ✅ `npm run lint` completes with 0 errors/warnings
- ✅ `npx tsc --noEmit` completes with 0 errors
- ✅ No console errors in browser developer tools

**AC8: Git Initialized**
- ✅ `.git` directory exists
- ✅ `.gitignore` configured (excludes node_modules, .next, data/*.db)
- ✅ Initial commit created

---

## Traceability Mapping

| Acceptance Criteria | Spec Section | Component/API | Test Idea |
|---------------------|--------------|---------------|-----------|
| AC1: Next.js initialized | Services and Modules → Next.js App Router | `/app/` directory | Manual: Run `npm run dev`, verify http://localhost:3000 loads |
| AC2: TypeScript valid | Data Models → Configuration Files | `tsconfig.json` | Automated: `npx tsc --noEmit` in CI/validation script |
| AC3: Tailwind configured | Data Models → Configuration Files | `tailwind.config.ts` | Manual: Verify test button color matches #007acb |
| AC4: shadcn/ui installed | Services and Modules → shadcn/ui CLI | `/components/ui/*` | Manual: Import Button component, verify no errors |
| AC5: Database setup | Data Models → Task Entity Schema | `./data/tasks.db` | Manual: Open Drizzle Studio, inspect tasks table |
| AC6: Migrations applied | Workflows → Step 7 | `drizzle/0000_initial.sql` | Automated: Run migrations in fresh environment, verify success |
| AC7: Quality gates pass | NFR → Build Reliability | ESLint + TypeScript | Automated: Run lint + type-check in pre-commit hook |
| AC8: Git initialized | Workflows → Step 9 | `.git/` | Manual: Run `git log`, verify initial commit exists |

**PRD Requirements Coverage:**
- NFR3 (TypeScript strict mode): AC2
- NFR4 (ESLint validation): AC7
- Architecture Section 2.1 (Technology Stack): AC1, AC5
- Architecture Section 5.1 (Database Schema): AC5, AC6

---

## Risks, Assumptions, Open Questions

### Risks

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| better-sqlite3 build fails on macOS/Windows | Low | High | Document platform-specific build requirements; provide pre-built binaries fallback |
| Next.js 15 version conflicts with dependencies | Low | Medium | Pin exact versions in package.json; test on clean install |
| Tailwind config overwrites Illustrate colors during updates | Low | Low | Lock tailwind.config.ts in Git; add validation test for primary-500 color |
| Node.js version mismatch | Medium | High | Document Node 20.x LTS requirement in README; add .nvmrc file |
| Migration fails on existing database | Low | Medium | Add migration rollback instructions; provide database reset script |

### Assumptions

1. **Development Environment:** Developers have Node.js 20.x installed (or will install via nvm)
2. **Operating System:** macOS, Linux, or Windows with WSL2 (better-sqlite3 compiles on all platforms)
3. **Git Installation:** Git is installed and configured globally
4. **Network Access:** npm registry accessible for dependency installation
5. **Disk Space:** At least 500 MB available for node_modules and build artifacts
6. **Permissions:** Write access to project directory for creating database files

### Open Questions

| Question | Decision Needed By | Impact | Status |
|----------|--------------------|---------| -------|
| Should we use pnpm instead of npm for faster installs? | Implementation start | Low (affects install speed only) | Use npm (standard, documented in architecture) |
| Should we add Prettier configuration in Epic 1? | Implementation start | Low (code style consistency) | Defer to Epic 6 (polish) |
| Should we create a seed script for development data? | Epic 2 start | Low (developer convenience) | Yes, add in Epic 2 |
| Should .env.local be created for database path? | Epic 1 | Low (configuration flexibility) | No, use hardcoded ./data/tasks.db |

---

## Test Strategy Summary

### Manual Testing (Required for Epic 1)

**Test Suite 1: Project Initialization**
1. Delete `node_modules` and `package-lock.json`
2. Run `npm install`
3. Verify installation completes without errors
4. Verify `node_modules` contains ~500+ packages

**Test Suite 2: Development Server**
1. Run `npm run dev`
2. Open `http://localhost:3000`
3. Verify Next.js default page displays
4. Verify no console errors in browser DevTools
5. Make change to `app/page.tsx`, save
6. Verify hot module replacement (HMR) triggers
7. Verify change reflected in browser

**Test Suite 3: TypeScript Validation**
1. Run `npx tsc --noEmit`
2. Verify output: "0 errors"
3. Add intentional type error to `app/page.tsx` (e.g., `const x: string = 123;`)
4. Run `npx tsc --noEmit`
5. Verify error detected
6. Revert error

**Test Suite 4: Database Setup**
1. Delete `data/` directory
2. Run `npx drizzle-kit generate`
3. Verify `drizzle/0000_initial.sql` created
4. Run `npx drizzle-kit migrate`
5. Verify `data/tasks.db` file created
6. Run `npx drizzle-kit studio`
7. Open `http://localhost:4983`
8. Verify `tasks` table visible with 7 columns
9. Manually insert test row via Drizzle Studio
10. Verify row persists after refresh

**Test Suite 5: Tailwind Colors**
1. Edit `app/page.tsx`, add:
   ```tsx
   <button className="bg-primary-500 text-white px-4 py-2 rounded-md">Test</button>
   ```
2. Open `http://localhost:3000`
3. Inspect button background color in DevTools
4. Verify computed color: `rgb(0, 122, 203)` (equivalent to #007acb)
5. Remove test button

**Test Suite 6: shadcn/ui Components**
1. Edit `app/page.tsx`, add:
   ```tsx
   import { Button } from '@/components/ui/button';
   <Button>Test Button</Button>
   ```
2. Verify no import errors
3. Verify button renders with shadcn/ui styling
4. Remove test code

### Automated Testing

**No unit tests or E2E tests required for Epic 1** (foundation setup only).

Testing deferred to Epic 7 (Testing and Quality Assurance).

### Validation Checklist

Before marking Epic 1 complete, run this validation:

```bash
# Terminal commands
npm run dev          # Should start on :3000 without errors
npx tsc --noEmit     # Should output 0 errors
npm run lint         # Should output 0 warnings/errors
npx drizzle-kit studio  # Should open on :4983

# Manual checks
# ✅ Database file exists at ./data/tasks.db
# ✅ All 6 shadcn/ui components in components/ui/
# ✅ tailwind.config.ts has primary-500: '#007acb'
# ✅ Git initialized with initial commit
```

**Expected Results:** All commands succeed with 0 errors.

---

## Appendix A: Complete Configuration Files

### A.1 TypeScript Configuration (`tsconfig.json`)

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["ES2022", "DOM", "DOM.Iterable"],
    "jsx": "preserve",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "allowJs": true,
    "strict": true,
    "noEmit": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "incremental": true,
    "paths": {
      "@/*": ["./*"]
    },
    "plugins": [
      {
        "name": "next"
      }
    ]
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx", ".next/types/**/*.ts"],
  "exclude": ["node_modules"]
}
```

### A.2 Tailwind Configuration (`tailwind.config.ts`)

```typescript
import type { Config } from 'tailwindcss';

const config: Config = {
  darkMode: ['class'],
  content: [
    './pages/**/*.{ts,tsx}',
    './components/**/*.{ts,tsx}',
    './app/**/*.{ts,tsx}',
  ],
  theme: {
    extend: {
      colors: {
        // Illustrate Inc. Brand Colors (from UX spec Section 5.1)
        primary: {
          50: '#e6f4fc',
          100: '#d9ebf7',
          200: '#b3d7ef',
          300: '#66b3e0',
          400: '#338fc8',
          500: '#007acb',   // Illustrate primary blue
          600: '#066ebc',   // Illustrate secondary blue
          700: '#055a97',
          800: '#044772',
          900: '#023554',
        },
        accent: {
          yellow: '#ffb81c',
          green: '#66cc33',
          red: '#b33630',
        },
        neutral: {
          50: '#ffffff',
          100: '#f4f4f4',
          200: '#e5e5e5',
          300: '#d7d7d7',
          400: '#a0a0a0',
          500: '#707070',
          600: '#555555',
          700: '#383838',
          800: '#272727',
          900: '#1a1a1a',
        },
        success: {
          light: '#e6f7e6',
          DEFAULT: '#66cc33',
          dark: '#52a329',
        },
        error: {
          light: '#fde8e7',
          DEFAULT: '#b33630',
          dark: '#8f2b26',
        },
        warning: {
          light: '#fff4e5',
          DEFAULT: '#ffb81c',
          dark: '#cc9316',
        },
      },
      borderRadius: {
        lg: '12px',
        md: '8px',
        sm: '6px',
      },
    },
  },
  plugins: [require('tailwindcss-animate')],
};

export default config;
```

### A.3 Drizzle ORM Configuration (`drizzle.config.ts`)

```typescript
import type { Config } from 'drizzle-kit';

export default {
  schema: './db/schema.ts',
  out: './drizzle',
  dialect: 'sqlite',
  dbCredentials: {
    url: './data/tasks.db',
  },
} satisfies Config;
```

### A.4 Database Schema (`db/schema.ts`)

```typescript
import { sqliteTable, text, integer, index } from 'drizzle-orm/sqlite-core';

export const tasks = sqliteTable(
  'tasks',
  {
    id: text('id').primaryKey(),
    title: text('title').notNull(),
    note: text('note'),
    status: text('status', { enum: ['todo', 'doing', 'done'] })
      .notNull()
      .default('todo'),
    createdAt: integer('created_at', { mode: 'timestamp_ms' }).notNull(),
    updatedAt: integer('updated_at', { mode: 'timestamp_ms' }).notNull(),
    completedAt: integer('completed_at', { mode: 'timestamp_ms' }),
  },
  (table) => ({
    statusIdx: index('idx_tasks_status').on(table.status),
    createdAtIdx: index('idx_tasks_created_at').on(table.createdAt),
    completedAtIdx: index('idx_tasks_completed_at').on(table.completedAt),
  })
);

export type Task = typeof tasks.$inferSelect;
export type NewTask = typeof tasks.$inferInsert;
```

### A.5 Database Connection (`db/index.ts`)

```typescript
import Database from 'better-sqlite3';
import { drizzle } from 'drizzle-orm/better-sqlite3';
import * as schema from './schema';

const sqlite = new Database('./data/tasks.db');
export const db = drizzle(sqlite, { schema });
```

### A.6 ESLint Configuration (`.eslintrc.json`)

```json
{
  "extends": [
    "next/core-web-vitals",
    "next/typescript"
  ],
  "rules": {
    "@typescript-eslint/no-unused-vars": ["error", {
      "argsIgnorePattern": "^_",
      "varsIgnorePattern": "^_"
    }],
    "@typescript-eslint/no-explicit-any": "error"
  }
}
```

### A.7 shadcn/ui Configuration (`components.json`)

```json
{
  "$schema": "https://ui.shadcn.com/schema.json",
  "style": "default",
  "rsc": true,
  "tsx": true,
  "tailwind": {
    "config": "tailwind.config.ts",
    "css": "app/globals.css",
    "baseColor": "neutral",
    "cssVariables": true
  },
  "aliases": {
    "components": "@/components",
    "utils": "@/lib/utils"
  }
}
```

### A.8 Git Ignore (`.gitignore`)

```
# Dependencies
/node_modules
/.pnp
.pnp.js

# Testing
/coverage

# Next.js
/.next/
/out/

# Production
/build

# Misc
.DS_Store
*.pem

# Debug
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Local env files
.env*.local

# Vercel
.vercel

# TypeScript
*.tsbuildinfo
next-env.d.ts

# Database
/data/*.db
/data/*.db-shm
/data/*.db-wal

# Drizzle
/drizzle/*.sql
```

---

## Appendix B: Generated Migration SQL

### B.1 Initial Migration (`drizzle/0000_initial.sql`)

```sql
CREATE TABLE `tasks` (
  `id` text PRIMARY KEY NOT NULL,
  `title` text NOT NULL,
  `note` text,
  `status` text DEFAULT 'todo' NOT NULL,
  `created_at` integer NOT NULL,
  `updated_at` integer NOT NULL,
  `completed_at` integer,
  CHECK(`status` IN ('todo', 'doing', 'done'))
);

CREATE INDEX `idx_tasks_status` ON `tasks` (`status`);
CREATE INDEX `idx_tasks_created_at` ON `tasks` (`created_at`);
CREATE INDEX `idx_tasks_completed_at` ON `tasks` (`completed_at`);
```

---

## Appendix C: Implementation Commands Reference

### C.1 Project Initialization

```bash
# Create Next.js project
npx create-next-app@latest personal-task-manager \
  --typescript \
  --tailwind \
  --app \
  --no-src-dir \
  --import-alias "@/*"

# Enter project directory
cd personal-task-manager
```

### C.2 shadcn/ui Setup

```bash
# Initialize shadcn/ui
npx shadcn@latest init

# Install base components
npx shadcn@latest add button
npx shadcn@latest add card
npx shadcn@latest add dialog
npx shadcn@latest add dropdown-menu
npx shadcn@latest add input
npx shadcn@latest add textarea
```

### C.3 Database Setup

```bash
# Install database dependencies
npm install drizzle-orm better-sqlite3
npm install -D drizzle-kit @types/better-sqlite3

# Create data directory
mkdir -p data

# Generate migration
npx drizzle-kit generate

# Apply migration
npx drizzle-kit migrate

# Open Drizzle Studio (optional)
npx drizzle-kit studio
```

### C.4 Validation Commands

```bash
# Start development server
npm run dev

# Type checking
npx tsc --noEmit

# Linting
npm run lint

# Build project (optional, for production-like testing)
npm run build
```

---

**Tech Spec Status:** ✅ Complete
**Generated:** 2025-10-21
**Author:** Kevin
**Epic:** 1-foundation (Foundation Setup)
**Estimated Effort:** 0.5 days

---

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>
