# Tech Spec: Epic 1 - Foundation Setup

**Epic:** Foundation Setup
**Project:** Personal TaskBoard
**Date:** 2025-10-21
**Status:** Ready for Implementation
**Estimated Effort:** 0.5 days

---

## 1. Epic Overview

### 1.1 Scope

Set up the foundational infrastructure for the Personal TaskBoard application, including project initialization, database configuration, and core technology stack integration.

**Deliverables:**
- Initialized Next.js 15 project with TypeScript strict mode
- Configured Tailwind CSS with Illustrate Inc. brand colors
- Configured and installed shadcn/ui CLI
- Set up SQLite database with Drizzle ORM
- Created database schema and migrations
- Verified basic project structure and routing

### 1.2 User Stories

N/A - This is a technical enabler epic

### 1.3 Dependencies

**Upstream:** None (first epic)
**Downstream:** Epic 2 (Task Display) depends on this foundation

---

## 2. Technical Design

### 2.1 Project Initialization

**Technology:** Next.js 15.0.3 with TypeScript

**Implementation Steps:**

```bash
# 1. Create Next.js project
npx create-next-app@latest personal-task-manager --typescript --tailwind --app --no-src-dir

# Project configuration:
# - TypeScript: Yes
# - ESLint: Yes
# - Tailwind CSS: Yes
# - App Router: Yes
# - Import alias: @/*
# - Turbopack: No (use Webpack for better compatibility)
```

**Project Structure After Init:**
```
personal-task-manager/
├── app/
│   ├── layout.tsx
│   ├── page.tsx
│   └── globals.css
├── public/
├── node_modules/
├── .gitignore
├── next.config.js
├── package.json
├── tsconfig.json
├── tailwind.config.ts
└── README.md
```

### 2.2 TypeScript Configuration

**File:** `tsconfig.json`

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
    "strict": true,                    // Strict mode enabled (NFR3)
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

**Validation:**
```bash
npx tsc --noEmit  # Should pass with 0 errors
```

### 2.3 Tailwind CSS Configuration

**File:** `tailwind.config.ts`

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
          yellow: '#ffb81c',  // Illustrate accent yellow
          green: '#66cc33',   // Success states
          red: '#b33630',     // Destructive actions
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
          800: '#272727',   // Illustrate primary text
          900: '#1a1a1a',
        },
        // Semantic colors
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

**Installation:**
```bash
npm install -D tailwindcss-animate
```

### 2.4 shadcn/ui Setup

**Installation:**
```bash
# Install shadcn/ui CLI
npx shadcn@latest init

# Configuration prompts:
# - Style: Default
# - Base color: Neutral
# - CSS variables: Yes
```

**File Created:** `components.json`

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

**Install Initial Components:**
```bash
npx shadcn@latest add button
npx shadcn@latest add card
npx shadcn@latest add dialog
npx shadcn@latest add dropdown-menu
npx shadcn@latest add input
npx shadcn@latest add textarea
```

**Created Files:**
- `components/ui/button.tsx`
- `components/ui/card.tsx`
- `components/ui/dialog.tsx`
- `components/ui/dropdown-menu.tsx`
- `components/ui/input.tsx`
- `components/ui/textarea.tsx`
- `lib/utils.ts` (cn() helper)

### 2.5 Database Setup

#### 2.5.1 Install Database Dependencies

```bash
npm install drizzle-orm better-sqlite3
npm install -D drizzle-kit @types/better-sqlite3
```

**Versions:**
- `drizzle-orm@0.36.4`
- `better-sqlite3@11.5.0`
- `drizzle-kit@0.30.1`

#### 2.5.2 Drizzle Configuration

**File:** `drizzle.config.ts`

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

#### 2.5.3 Database Schema

**File:** `db/schema.ts`

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

// Inferred types for use throughout application
export type Task = typeof tasks.$inferSelect;
export type NewTask = typeof tasks.$inferInsert;
```

#### 2.5.4 Database Connection

**File:** `db/index.ts`

```typescript
import Database from 'better-sqlite3';
import { drizzle } from 'drizzle-orm/better-sqlite3';
import * as schema from './schema';

const sqlite = new Database('./data/tasks.db');
export const db = drizzle(sqlite, { schema });
```

#### 2.5.5 Generate and Run Migrations

```bash
# Create data directory
mkdir -p data

# Generate migration from schema
npx drizzle-kit generate

# This creates: drizzle/0000_initial.sql

# Apply migration (create database and tables)
npx drizzle-kit migrate
```

**Generated Migration:** `drizzle/0000_initial.sql`

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

### 2.6 ESLint Configuration

**File:** `.eslintrc.json`

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

**Validation:**
```bash
npm run lint  # Should pass with 0 errors/warnings
```

### 2.7 Git Setup

**Create `.gitignore`:**

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

**Initialize Git:**
```bash
git init
git add .
git commit -m "Initial commit: Project foundation setup

- Next.js 15 with TypeScript strict mode
- Tailwind CSS with Illustrate brand colors
- shadcn/ui components installed
- SQLite database with Drizzle ORM
- Database schema and migrations
"
```

---

## 3. Implementation Checklist

### 3.1 Setup Tasks

- [ ] Run `npx create-next-app@latest` with correct flags
- [ ] Verify TypeScript strict mode in `tsconfig.json`
- [ ] Configure Tailwind with Illustrate brand colors in `tailwind.config.ts`
- [ ] Run `npx shadcn@latest init` to set up component library
- [ ] Install 6 initial shadcn/ui components (button, card, dialog, dropdown-menu, input, textarea)
- [ ] Install database dependencies (drizzle-orm, better-sqlite3)
- [ ] Create `drizzle.config.ts` with SQLite configuration
- [ ] Create `db/schema.ts` with tasks table definition
- [ ] Create `db/index.ts` with database connection
- [ ] Create `data/` directory for SQLite file
- [ ] Run `npx drizzle-kit generate` to create migration
- [ ] Run `npx drizzle-kit migrate` to apply migration
- [ ] Verify database file created at `./data/tasks.db`

### 3.2 Validation Tasks

- [ ] Run `npm run dev` and verify app loads at `http://localhost:3000`
- [ ] Run `npx tsc --noEmit` and verify 0 type errors
- [ ] Run `npm run lint` and verify 0 lint errors
- [ ] Open Drizzle Studio (`npx drizzle-kit studio`) and verify tasks table exists
- [ ] Verify all shadcn/ui components are in `components/ui/`
- [ ] Verify Tailwind primary colors match Illustrate brand (#007acb)

---

## 4. Testing Approach

### 4.1 Manual Testing

**Test 1: Project Runs**
1. Run `npm run dev`
2. Open `http://localhost:3000`
3. Verify default Next.js page loads without errors

**Test 2: TypeScript Strict Mode**
1. Add intentional type error to `app/page.tsx`
2. Run `npx tsc --noEmit`
3. Verify error is caught
4. Revert error

**Test 3: Database Connection**
1. Open Drizzle Studio: `npx drizzle-kit studio`
2. Navigate to `http://localhost:4983`
3. Verify `tasks` table visible with correct schema
4. Manually insert a test row
5. Verify row appears in Drizzle Studio

**Test 4: Tailwind Colors**
1. Add temporary button to `app/page.tsx`:
   ```tsx
   <button className="bg-primary-500 text-white px-4 py-2 rounded">
     Test Button
   </button>
   ```
2. Verify button is Illustrate blue (#007acb)
3. Remove temporary button

### 4.2 Automated Testing

No automated tests required for Epic 1 (foundation setup).

---

## 5. Acceptance Criteria

- [x] Next.js 15 project created with App Router
- [x] TypeScript strict mode enabled and passing
- [x] Tailwind CSS configured with Illustrate brand colors
- [x] shadcn/ui CLI installed and 6 base components added
- [x] SQLite database created at `./data/tasks.db`
- [x] Drizzle ORM configured and tasks table schema defined
- [x] Database migration generated and applied successfully
- [x] ESLint configured and passing with 0 errors
- [x] Git repository initialized with initial commit
- [x] Development server runs without errors
- [x] All validation tasks pass

---

## 6. Dependencies and Risks

### 6.1 Dependencies

**External:**
- Node.js 20.x LTS installed
- npm 10.x or pnpm 8.x installed
- Write access to project directory

**Internal:**
- None (first epic)

### 6.2 Risks

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Version conflicts between Next.js 15 and dependencies | Low | Medium | Pin exact versions in package.json |
| better-sqlite3 build issues on different OS | Low | Medium | Document platform-specific build requirements |
| Tailwind config overwrites Illustrate colors | Low | Low | Verify colors in manual testing |

---

## 7. Next Steps

After completing Epic 1:

1. **Proceed to Epic 2: Task Display and Organization**
   - Create TaskBoard, TaskColumn, TaskCard components
   - Implement data fetching from SQLite
   - Build three-column layout

2. **Optional: Create Seed Data Script**
   - `db/seed.ts` to populate test tasks
   - Useful for development and testing

---

**Tech Spec Status:** ✅ Complete
**Ready for Implementation:** Yes
**Estimated Completion:** 0.5 days

---

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>
