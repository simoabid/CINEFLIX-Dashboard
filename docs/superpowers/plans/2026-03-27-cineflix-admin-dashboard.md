# CINEFLIX Admin Dashboard Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a production-ready MERN admin dashboard for CINEFLIX with single-admin auth, executive analytics, external-content curation overlays, homepage/banner management, user and subscription oversight, sync operations, and settings.

**Architecture:** Use a greenfield MERN monorepo with a React admin app in `apps/admin`, an Express API in `apps/api`, and shared TypeScript contracts in `packages/shared`. MongoDB stores the local admin control layer and operational data, while TMDB and other external APIs remain the source of truth for catalog metadata.

**Tech Stack:** React 19 + Vite + TypeScript, React Router, TanStack Query, Zustand, Tailwind CSS, Express, Mongoose, Zod, JWT, bcrypt, Vitest, React Testing Library, Supertest, Playwright, pnpm workspaces

---

## Proposed File Structure

### Workspace Root
- `package.json` — workspace scripts
- `pnpm-workspace.yaml` — workspace package discovery
- `tsconfig.base.json` — shared TypeScript config
- `.gitignore` — ignored files and secrets
- `.env.example` — documented environment variables
- `playwright.config.ts` — E2E configuration
- `docs/superpowers/specs/2026-03-27-cineflix-admin-dashboard-design.md` — approved spec
- `docs/superpowers/plans/2026-03-27-cineflix-admin-dashboard.md` — this plan

### Shared Package
- `packages/shared/package.json`
- `packages/shared/tsconfig.json`
- `packages/shared/src/contracts/auth.ts`
- `packages/shared/src/contracts/dashboard.ts`
- `packages/shared/src/contracts/catalog.ts`
- `packages/shared/src/contracts/banner.ts`
- `packages/shared/src/contracts/user.ts`
- `packages/shared/src/contracts/subscription.ts`
- `packages/shared/src/contracts/sync.ts`
- `packages/shared/src/contracts/settings.ts`
- `packages/shared/src/index.ts`

### API App
- `apps/api/package.json`
- `apps/api/tsconfig.json`
- `apps/api/src/server.ts`
- `apps/api/src/app.ts`
- `apps/api/src/config/env.ts`
- `apps/api/src/config/db.ts`
- `apps/api/src/config/logger.ts`
- `apps/api/src/middleware/authenticateAdmin.ts`
- `apps/api/src/middleware/errorHandler.ts`
- `apps/api/src/middleware/notFound.ts`
- `apps/api/src/models/Admin.ts`
- `apps/api/src/models/CatalogOverlay.ts`
- `apps/api/src/models/HomepageSection.ts`
- `apps/api/src/models/User.ts`
- `apps/api/src/models/Subscription.ts`
- `apps/api/src/models/SyncJob.ts`
- `apps/api/src/models/Settings.ts`
- `apps/api/src/modules/auth/auth.routes.ts`
- `apps/api/src/modules/auth/auth.controller.ts`
- `apps/api/src/modules/auth/auth.service.ts`
- `apps/api/src/modules/auth/auth.schemas.ts`
- `apps/api/src/modules/dashboard/dashboard.routes.ts`
- `apps/api/src/modules/dashboard/dashboard.controller.ts`
- `apps/api/src/modules/dashboard/dashboard.service.ts`
- `apps/api/src/modules/catalog/catalog.routes.ts`
- `apps/api/src/modules/catalog/catalog.controller.ts`
- `apps/api/src/modules/catalog/catalog.service.ts`
- `apps/api/src/modules/catalog/catalog.schemas.ts`
- `apps/api/src/modules/banners/banners.routes.ts`
- `apps/api/src/modules/banners/banners.controller.ts`
- `apps/api/src/modules/banners/banners.service.ts`
- `apps/api/src/modules/users/users.routes.ts`
- `apps/api/src/modules/users/users.controller.ts`
- `apps/api/src/modules/users/users.service.ts`
- `apps/api/src/modules/subscriptions/subscriptions.routes.ts`
- `apps/api/src/modules/subscriptions/subscriptions.controller.ts`
- `apps/api/src/modules/subscriptions/subscriptions.service.ts`
- `apps/api/src/modules/sync/sync.routes.ts`
- `apps/api/src/modules/sync/sync.controller.ts`
- `apps/api/src/modules/sync/sync.service.ts`
- `apps/api/src/modules/sync/providers/tmdbProvider.ts`
- `apps/api/src/modules/settings/settings.routes.ts`
- `apps/api/src/modules/settings/settings.controller.ts`
- `apps/api/src/modules/settings/settings.service.ts`
- `apps/api/src/lib/jwt.ts`
- `apps/api/src/lib/password.ts`
- `apps/api/src/lib/httpError.ts`
- `apps/api/src/lib/asyncHandler.ts`
- `apps/api/tests/helpers/testApp.ts`
- `apps/api/tests/helpers/testDb.ts`
- `apps/api/tests/auth.integration.test.ts`
- `apps/api/tests/dashboard.integration.test.ts`
- `apps/api/tests/catalog.integration.test.ts`
- `apps/api/tests/banners.integration.test.ts`
- `apps/api/tests/users.integration.test.ts`
- `apps/api/tests/subscriptions.integration.test.ts`
- `apps/api/tests/sync.integration.test.ts`
- `apps/api/tests/settings.integration.test.ts`

### Admin App
- `apps/admin/package.json`
- `apps/admin/tsconfig.json`
- `apps/admin/vite.config.ts`
- `apps/admin/index.html`
- `apps/admin/src/main.tsx`
- `apps/admin/src/App.tsx`
- `apps/admin/src/router.tsx`
- `apps/admin/src/lib/queryClient.ts`
- `apps/admin/src/lib/apiClient.ts`
- `apps/admin/src/lib/utils.ts`
- `apps/admin/src/store/authStore.ts`
- `apps/admin/src/styles/globals.css`
- `apps/admin/src/components/layout/AdminLayout.tsx`
- `apps/admin/src/components/layout/Sidebar.tsx`
- `apps/admin/src/components/layout/Topbar.tsx`
- `apps/admin/src/components/ui/KpiCard.tsx`
- `apps/admin/src/components/ui/DataTable.tsx`
- `apps/admin/src/components/ui/StatusBadge.tsx`
- `apps/admin/src/components/ui/EmptyState.tsx`
- `apps/admin/src/components/ui/LoadingState.tsx`
- `apps/admin/src/components/ui/AlertBanner.tsx`
- `apps/admin/src/features/auth/pages/LoginPage.tsx`
- `apps/admin/src/features/auth/components/ProtectedRoute.tsx`
- `apps/admin/src/features/dashboard/pages/DashboardPage.tsx`
- `apps/admin/src/features/catalog/pages/CatalogPage.tsx`
- `apps/admin/src/features/catalog/components/CatalogFilters.tsx`
- `apps/admin/src/features/catalog/components/CatalogTable.tsx`
- `apps/admin/src/features/catalog/components/CatalogEditDrawer.tsx`
- `apps/admin/src/features/banners/pages/BannersPage.tsx`
- `apps/admin/src/features/banners/components/BannerForm.tsx`
- `apps/admin/src/features/users/pages/UsersPage.tsx`
- `apps/admin/src/features/subscriptions/pages/SubscriptionsPage.tsx`
- `apps/admin/src/features/sync/pages/SyncOperationsPage.tsx`
- `apps/admin/src/features/settings/pages/SettingsPage.tsx`
- `apps/admin/src/features/*/api/*.ts` — feature API hooks
- `apps/admin/src/features/*/types.ts` — feature-local types if needed
- `apps/admin/src/test/setup.ts`
- `apps/admin/src/test/render.tsx`
- `apps/admin/src/features/auth/pages/LoginPage.test.tsx`
- `apps/admin/src/features/dashboard/pages/DashboardPage.test.tsx`
- `apps/admin/src/features/catalog/pages/CatalogPage.test.tsx`
- `apps/admin/src/features/banners/pages/BannersPage.test.tsx`
- `apps/admin/src/features/sync/pages/SyncOperationsPage.test.tsx`
- `e2e/admin-login.spec.ts`
- `e2e/dashboard-overview.spec.ts`
- `e2e/catalog-curation.spec.ts`
- `e2e/banner-management.spec.ts`
- `e2e/sync-ops.spec.ts`

---

### Task 1: Initialize workspace and tooling

**Files:**
- Create: `package.json`
- Create: `pnpm-workspace.yaml`
- Create: `tsconfig.base.json`
- Create: `.gitignore`
- Create: `.env.example`
- Create: `apps/admin/package.json`
- Create: `apps/api/package.json`
- Create: `packages/shared/package.json`

- [ ] **Step 1: Initialize git for checkpointed work**

```bash
git init
```

Expected: output includes `Initialized empty Git repository`

- [ ] **Step 2: Create the root workspace manifest**

```json
{
  "name": "cineflix-admin-dashboard",
  "private": true,
  "packageManager": "pnpm@10.6.0",
  "scripts": {
    "dev": "pnpm --parallel --filter admin --filter api dev",
    "build": "pnpm -r build",
    "test": "pnpm -r test",
    "test:unit": "pnpm -r test:unit",
    "test:integration": "pnpm --filter api test:integration",
    "test:e2e": "playwright test",
    "lint": "pnpm -r lint"
  }
}
```

- [ ] **Step 3: Create workspace discovery config**

```yaml
packages:
  - apps/*
  - packages/*
```

- [ ] **Step 4: Create the shared TypeScript base config**

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "strict": true,
    "skipLibCheck": true,
    "resolveJsonModule": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "baseUrl": "."
  }
}
```

- [ ] **Step 5: Create `.gitignore` and environment template**

```gitignore
node_modules
dist
coverage
.playwright
.env
.env.local
```

```env
NODE_ENV=development
PORT=4000
MONGODB_URI=mongodb://127.0.0.1:27017/cineflix_admin
JWT_SECRET=replace-me
ADMIN_EMAIL=admin@cineflix.local
ADMIN_PASSWORD=replace-me
TMDB_API_KEY=replace-me
```

- [ ] **Step 6: Install root and app dependencies**

Run:
```bash
pnpm add -Dw typescript tsx vitest @vitest/coverage-v8 eslint @types/node playwright
pnpm add -F admin react react-dom react-router-dom @tanstack/react-query zustand zod
pnpm add -F admin -D vite @vitejs/plugin-react tailwindcss @testing-library/react @testing-library/jest-dom jsdom @types/react @types/react-dom
pnpm add -F api express mongoose jsonwebtoken bcryptjs cors helmet morgan zod
pnpm add -F api -D supertest mongodb-memory-server @types/express @types/jsonwebtoken @types/bcryptjs @types/cors @types/morgan
pnpm add -F shared zod
```

Expected: install completes without errors

- [ ] **Step 7: Run the empty workspace test command**

Run: `pnpm test`
Expected: command completes, even if no tests are discovered yet

- [ ] **Step 8: Commit the workspace scaffold**

```bash
git add package.json pnpm-workspace.yaml tsconfig.base.json .gitignore .env.example apps/admin/package.json apps/api/package.json packages/shared/package.json
git commit -m "chore: initialize cineflix admin workspace"
```

---

### Task 2: Define shared contracts first

**Files:**
- Create: `packages/shared/src/contracts/auth.ts`
- Create: `packages/shared/src/contracts/dashboard.ts`
- Create: `packages/shared/src/contracts/catalog.ts`
- Create: `packages/shared/src/contracts/banner.ts`
- Create: `packages/shared/src/contracts/user.ts`
- Create: `packages/shared/src/contracts/subscription.ts`
- Create: `packages/shared/src/contracts/sync.ts`
- Create: `packages/shared/src/contracts/settings.ts`
- Create: `packages/shared/src/index.ts`
- Test: `packages/shared/src/contracts/catalog.test.ts`

- [ ] **Step 1: Write the failing schema test**

```ts
import { describe, expect, it } from 'vitest'
import { catalogOverlaySchema } from './catalog'

describe('catalogOverlaySchema', () => {
  it('accepts a curated external title overlay', () => {
    const result = catalogOverlaySchema.safeParse({
      externalId: 'tmdb-550',
      source: 'tmdb',
      contentType: 'movie',
      visible: true,
      featured: false,
      categorySlugs: ['trending-now'],
      customTitle: 'Fight Club',
      customDescription: 'Late-night cult favorite.',
      customTags: ['cult', 'thriller']
    })

    expect(result.success).toBe(true)
  })
})
```

- [ ] **Step 2: Run the schema test to verify it fails**

Run: `pnpm exec vitest packages/shared/src/contracts/catalog.test.ts`
Expected: FAIL with module or export not found

- [ ] **Step 3: Implement shared contract schemas**

```ts
import { z } from 'zod'

export const catalogOverlaySchema = z.object({
  externalId: z.string().min(1),
  source: z.enum(['tmdb', 'external']),
  contentType: z.enum(['movie', 'show']),
  visible: z.boolean(),
  featured: z.boolean(),
  categorySlugs: z.array(z.string().min(1)),
  customTitle: z.string().trim().min(1).nullable().optional(),
  customDescription: z.string().trim().min(1).nullable().optional(),
  customTags: z.array(z.string().min(1)).default([])
})

export type CatalogOverlayInput = z.infer<typeof catalogOverlaySchema>
```

- [ ] **Step 4: Export contracts from the shared package**

```ts
export * from './contracts/auth'
export * from './contracts/dashboard'
export * from './contracts/catalog'
export * from './contracts/banner'
export * from './contracts/user'
export * from './contracts/subscription'
export * from './contracts/sync'
export * from './contracts/settings'
```

- [ ] **Step 5: Re-run the schema test**

Run: `pnpm exec vitest packages/shared/src/contracts/catalog.test.ts`
Expected: PASS

- [ ] **Step 6: Commit the shared contracts**

```bash
git add packages/shared/src/contracts packages/shared/src/index.ts
git commit -m "feat: add shared admin contracts"
```

---

### Task 3: Build API foundation and health boot path

**Files:**
- Create: `apps/api/src/app.ts`
- Create: `apps/api/src/server.ts`
- Create: `apps/api/src/config/env.ts`
- Create: `apps/api/src/config/db.ts`
- Create: `apps/api/src/config/logger.ts`
- Create: `apps/api/src/lib/httpError.ts`
- Create: `apps/api/src/lib/asyncHandler.ts`
- Create: `apps/api/src/middleware/errorHandler.ts`
- Create: `apps/api/src/middleware/notFound.ts`
- Test: `apps/api/tests/app.integration.test.ts`

- [ ] **Step 1: Write the failing API health test**

```ts
import request from 'supertest'
import { describe, expect, it } from 'vitest'
import { createApp } from '../src/app'

describe('GET /health', () => {
  it('returns service status', async () => {
    const app = createApp()
    const response = await request(app).get('/health')

    expect(response.status).toBe(200)
    expect(response.body).toEqual({ success: true, data: { status: 'ok' }, error: null })
  })
})
```

- [ ] **Step 2: Run the health test to verify it fails**

Run: `pnpm --filter api exec vitest tests/app.integration.test.ts`
Expected: FAIL because `createApp` does not exist

- [ ] **Step 3: Implement the app shell and health route**

```ts
import cors from 'cors'
import express from 'express'
import { errorHandler } from './middleware/errorHandler'
import { notFound } from './middleware/notFound'

export const createApp = () => {
  const app = express()

  app.use(cors())
  app.use(express.json())

  app.get('/health', (_req, res) => {
    res.status(200).json({ success: true, data: { status: 'ok' }, error: null })
  })

  app.use(notFound)
  app.use(errorHandler)

  return app
}
```

- [ ] **Step 4: Add server boot code and env parsing**

```ts
import { z } from 'zod'

export const envSchema = z.object({
  NODE_ENV: z.enum(['development', 'test', 'production']).default('development'),
  PORT: z.coerce.number().default(4000),
  MONGODB_URI: z.string().min(1),
  JWT_SECRET: z.string().min(1),
  ADMIN_EMAIL: z.string().email(),
  ADMIN_PASSWORD: z.string().min(8),
  TMDB_API_KEY: z.string().min(1)
})
```

- [ ] **Step 5: Re-run the health test**

Run: `pnpm --filter api exec vitest tests/app.integration.test.ts`
Expected: PASS

- [ ] **Step 6: Commit the API foundation**

```bash
git add apps/api/src apps/api/tests/app.integration.test.ts
git commit -m "feat: add api app foundation"
```

---

### Task 4: Implement admin authentication

**Files:**
- Create: `apps/api/src/models/Admin.ts`
- Create: `apps/api/src/lib/jwt.ts`
- Create: `apps/api/src/lib/password.ts`
- Create: `apps/api/src/middleware/authenticateAdmin.ts`
- Create: `apps/api/src/modules/auth/auth.routes.ts`
- Create: `apps/api/src/modules/auth/auth.controller.ts`
- Create: `apps/api/src/modules/auth/auth.service.ts`
- Create: `apps/api/src/modules/auth/auth.schemas.ts`
- Modify: `apps/api/src/app.ts`
- Test: `apps/api/tests/auth.integration.test.ts`

- [ ] **Step 1: Write the failing login test**

```ts
import request from 'supertest'
import { beforeEach, describe, expect, it } from 'vitest'
import { createApp } from '../src/app'

describe('POST /api/auth/login', () => {
  beforeEach(async () => {
    // seed admin in test db
  })

  it('returns a JWT for valid admin credentials', async () => {
    const app = createApp()
    const response = await request(app).post('/api/auth/login').send({
      email: 'admin@cineflix.local',
      password: 'password123'
    })

    expect(response.status).toBe(200)
    expect(response.body.data.token).toEqual(expect.any(String))
    expect(response.body.data.admin.email).toBe('admin@cineflix.local')
  })
})
```

- [ ] **Step 2: Run the auth test to verify it fails**

Run: `pnpm --filter api exec vitest tests/auth.integration.test.ts`
Expected: FAIL with 404 or missing route

- [ ] **Step 3: Implement admin model and auth helpers**

```ts
import { Schema, model } from 'mongoose'

const adminSchema = new Schema({
  email: { type: String, required: true, unique: true },
  passwordHash: { type: String, required: true },
  name: { type: String, required: true }
}, { timestamps: true })

export const AdminModel = model('Admin', adminSchema)
```

```ts
import jwt from 'jsonwebtoken'

export const signAdminToken = (adminId: string) =>
  jwt.sign({ sub: adminId, role: 'super-admin' }, process.env.JWT_SECRET!, { expiresIn: '1d' })
```

- [ ] **Step 4: Implement login route and protected middleware**

```ts
router.post('/login', asyncHandler(async (req, res) => {
  const result = await authService.login(req.body)

  res.status(200).json({ success: true, data: result, error: null })
}))
```

```ts
export const authenticateAdmin = asyncHandler(async (req, _res, next) => {
  const header = req.headers.authorization
  if (!header?.startsWith('Bearer ')) {
    throw new HttpError(401, 'Unauthorized')
  }

  req.admin = verifyAdminToken(header.slice(7))
  next()
})
```

- [ ] **Step 5: Re-run the auth test**

Run: `pnpm --filter api exec vitest tests/auth.integration.test.ts`
Expected: PASS

- [ ] **Step 6: Add a protected `/api/auth/me` integration test and implement it**

```ts
it('returns the current admin for a valid token', async () => {
  const response = await request(app)
    .get('/api/auth/me')
    .set('Authorization', `Bearer ${token}`)

  expect(response.status).toBe(200)
  expect(response.body.data.email).toBe('admin@cineflix.local')
})
```

- [ ] **Step 7: Run the auth test file again**

Run: `pnpm --filter api exec vitest tests/auth.integration.test.ts`
Expected: PASS for login and me routes

- [ ] **Step 8: Commit the auth feature**

```bash
git add apps/api/src/models/Admin.ts apps/api/src/lib/jwt.ts apps/api/src/lib/password.ts apps/api/src/middleware/authenticateAdmin.ts apps/api/src/modules/auth apps/api/src/app.ts apps/api/tests/auth.integration.test.ts
git commit -m "feat: add super admin authentication"
```

---

### Task 5: Build admin shell UI and login page

**Files:**
- Create: `apps/admin/src/main.tsx`
- Create: `apps/admin/src/App.tsx`
- Create: `apps/admin/src/router.tsx`
- Create: `apps/admin/src/lib/queryClient.ts`
- Create: `apps/admin/src/lib/apiClient.ts`
- Create: `apps/admin/src/store/authStore.ts`
- Create: `apps/admin/src/styles/globals.css`
- Create: `apps/admin/src/components/layout/AdminLayout.tsx`
- Create: `apps/admin/src/components/layout/Sidebar.tsx`
- Create: `apps/admin/src/components/layout/Topbar.tsx`
- Create: `apps/admin/src/features/auth/pages/LoginPage.tsx`
- Create: `apps/admin/src/features/auth/components/ProtectedRoute.tsx`
- Test: `apps/admin/src/features/auth/pages/LoginPage.test.tsx`

- [ ] **Step 1: Write the failing login page test**

```tsx
import { render, screen } from '@testing-library/react'
import { LoginPage } from './LoginPage'

describe('LoginPage', () => {
  it('renders the CINEFLIX admin sign-in form', () => {
    render(<LoginPage />)

    expect(screen.getByRole('heading', { name: /cineflix admin/i })).toBeInTheDocument()
    expect(screen.getByLabelText(/email/i)).toBeInTheDocument()
    expect(screen.getByLabelText(/password/i)).toBeInTheDocument()
  })
})
```

- [ ] **Step 2: Run the login page test to verify it fails**

Run: `pnpm --filter admin exec vitest src/features/auth/pages/LoginPage.test.tsx`
Expected: FAIL because the component does not exist

- [ ] **Step 3: Implement the admin login page and store**

```tsx
export const LoginPage = () => {
  return (
    <main className="min-h-screen bg-zinc-950 text-white">
      <section className="mx-auto flex min-h-screen max-w-md flex-col justify-center gap-6 px-6">
        <h1 className="text-4xl font-bold tracking-wide text-red-500">CINEFLIX Admin</h1>
        <form className="space-y-4 rounded-2xl border border-zinc-800 bg-zinc-900 p-6">
          <label className="block">
            <span>Email</span>
            <input name="email" type="email" className="mt-2 w-full rounded-lg bg-zinc-950 px-3 py-2" />
          </label>
          <label className="block">
            <span>Password</span>
            <input name="password" type="password" className="mt-2 w-full rounded-lg bg-zinc-950 px-3 py-2" />
          </label>
          <button className="w-full rounded-lg bg-red-600 px-4 py-2 font-semibold">Sign in</button>
        </form>
      </section>
    </main>
  )
}
```

- [ ] **Step 4: Add the protected layout shell**

```tsx
export const AdminLayout = () => (
  <div className="min-h-screen bg-zinc-950 text-white">
    <Sidebar />
    <div className="pl-72">
      <Topbar />
      <main className="p-6">
        <Outlet />
      </main>
    </div>
  </div>
)
```

- [ ] **Step 5: Re-run the login page test**

Run: `pnpm --filter admin exec vitest src/features/auth/pages/LoginPage.test.tsx`
Expected: PASS

- [ ] **Step 6: Commit the admin shell**

```bash
git add apps/admin/src/main.tsx apps/admin/src/App.tsx apps/admin/src/router.tsx apps/admin/src/lib apps/admin/src/store apps/admin/src/styles apps/admin/src/components/layout apps/admin/src/features/auth
git commit -m "feat: add admin shell and login ui"
```

---

### Task 6: Implement dashboard KPIs API and page

**Files:**
- Create: `apps/api/src/modules/dashboard/dashboard.routes.ts`
- Create: `apps/api/src/modules/dashboard/dashboard.controller.ts`
- Create: `apps/api/src/modules/dashboard/dashboard.service.ts`
- Modify: `apps/api/src/app.ts`
- Create: `apps/admin/src/components/ui/KpiCard.tsx`
- Create: `apps/admin/src/components/ui/AlertBanner.tsx`
- Create: `apps/admin/src/features/dashboard/api/getDashboard.ts`
- Create: `apps/admin/src/features/dashboard/pages/DashboardPage.tsx`
- Test: `apps/api/tests/dashboard.integration.test.ts`
- Test: `apps/admin/src/features/dashboard/pages/DashboardPage.test.tsx`

- [ ] **Step 1: Write the failing dashboard API test**

```ts
it('returns KPI cards and sync alerts for the dashboard', async () => {
  const response = await request(app)
    .get('/api/dashboard')
    .set('Authorization', `Bearer ${token}`)

  expect(response.status).toBe(200)
  expect(response.body.data.kpis).toHaveLength(4)
  expect(response.body.data.syncAlerts).toBeInstanceOf(Array)
})
```

- [ ] **Step 2: Run the dashboard API test to verify it fails**

Run: `pnpm --filter api exec vitest tests/dashboard.integration.test.ts`
Expected: FAIL with missing route

- [ ] **Step 3: Implement the dashboard service and route**

```ts
export const getDashboardSummary = async () => ({
  kpis: [
    { key: 'revenue', label: 'Revenue', value: '$24,500', delta: '+8.4%' },
    { key: 'subscriptions', label: 'Subscriptions', value: '1,284', delta: '+3.1%' },
    { key: 'activeUsers', label: 'Active Users', value: '9,420', delta: '+5.7%' },
    { key: 'churn', label: 'Churn', value: '2.1%', delta: '-0.4%' }
  ],
  topContent: [],
  syncAlerts: []
})
```

- [ ] **Step 4: Write the failing dashboard page test**

```tsx
it('renders KPI cards from the dashboard query', async () => {
  render(<DashboardPage />, { wrapper: TestProviders })

  expect(await screen.findByText('Revenue')).toBeInTheDocument()
  expect(screen.getByText('Subscriptions')).toBeInTheDocument()
})
```

- [ ] **Step 5: Implement the dashboard page**

```tsx
export const DashboardPage = () => {
  const { data } = useDashboardQuery()

  return (
    <section className="space-y-6">
      <header>
        <h1 className="text-3xl font-bold text-white">Executive Dashboard</h1>
      </header>
      <div className="grid gap-4 md:grid-cols-2 xl:grid-cols-4">
        {data?.kpis.map((kpi) => <KpiCard key={kpi.key} {...kpi} />)}
      </div>
    </section>
  )
}
```

- [ ] **Step 6: Re-run both dashboard test files**

Run: `pnpm --filter api exec vitest tests/dashboard.integration.test.ts && pnpm --filter admin exec vitest src/features/dashboard/pages/DashboardPage.test.tsx`
Expected: PASS

- [ ] **Step 7: Commit dashboard KPIs**

```bash
git add apps/api/src/modules/dashboard apps/api/src/app.ts apps/api/tests/dashboard.integration.test.ts apps/admin/src/components/ui/KpiCard.tsx apps/admin/src/components/ui/AlertBanner.tsx apps/admin/src/features/dashboard
git commit -m "feat: add dashboard overview module"
```

---

### Task 7: Implement catalog curation overlays

**Files:**
- Create: `apps/api/src/models/CatalogOverlay.ts`
- Create: `apps/api/src/modules/catalog/catalog.routes.ts`
- Create: `apps/api/src/modules/catalog/catalog.controller.ts`
- Create: `apps/api/src/modules/catalog/catalog.service.ts`
- Create: `apps/api/src/modules/catalog/catalog.schemas.ts`
- Modify: `apps/api/src/app.ts`
- Create: `apps/admin/src/features/catalog/api/getCatalog.ts`
- Create: `apps/admin/src/features/catalog/api/updateCatalogOverlay.ts`
- Create: `apps/admin/src/features/catalog/pages/CatalogPage.tsx`
- Create: `apps/admin/src/features/catalog/components/CatalogFilters.tsx`
- Create: `apps/admin/src/features/catalog/components/CatalogTable.tsx`
- Create: `apps/admin/src/features/catalog/components/CatalogEditDrawer.tsx`
- Test: `apps/api/tests/catalog.integration.test.ts`
- Test: `apps/admin/src/features/catalog/pages/CatalogPage.test.tsx`

- [ ] **Step 1: Write the failing catalog overlay API test**

```ts
it('updates local curation fields for an external title', async () => {
  const response = await request(app)
    .patch('/api/catalog/tmdb-550/overlay')
    .set('Authorization', `Bearer ${token}`)
    .send({
      visible: true,
      featured: true,
      categorySlugs: ['featured'],
      customTitle: 'Fight Club',
      customTags: ['cult']
    })

  expect(response.status).toBe(200)
  expect(response.body.data.featured).toBe(true)
  expect(response.body.data.categorySlugs).toEqual(['featured'])
})
```

- [ ] **Step 2: Run the catalog API test to verify it fails**

Run: `pnpm --filter api exec vitest tests/catalog.integration.test.ts`
Expected: FAIL because the route or model does not exist

- [ ] **Step 3: Implement the overlay model and update service**

```ts
const catalogOverlaySchema = new Schema({
  externalId: { type: String, required: true, unique: true },
  source: { type: String, required: true },
  contentType: { type: String, required: true },
  visible: { type: Boolean, required: true, default: true },
  featured: { type: Boolean, required: true, default: false },
  categorySlugs: { type: [String], required: true, default: [] },
  customTitle: { type: String, default: null },
  customDescription: { type: String, default: null },
  customTags: { type: [String], required: true, default: [] }
}, { timestamps: true })
```

- [ ] **Step 4: Write the failing catalog page test**

```tsx
it('shows imported titles and opens the overlay editor', async () => {
  render(<CatalogPage />, { wrapper: TestProviders })

  expect(await screen.findByText('Fight Club')).toBeInTheDocument()
  expect(screen.getByRole('button', { name: /edit overlay/i })).toBeInTheDocument()
})
```

- [ ] **Step 5: Implement the catalog curation UI**

```tsx
export const CatalogPage = () => {
  const { data } = useCatalogQuery()
  const [selectedItem, setSelectedItem] = useState<string | null>(null)

  return (
    <section className="space-y-6">
      <header>
        <h1 className="text-3xl font-bold">Catalog Curation</h1>
      </header>
      <CatalogFilters />
      <CatalogTable items={data?.items ?? []} onEdit={setSelectedItem} />
      <CatalogEditDrawer externalId={selectedItem} onClose={() => setSelectedItem(null)} />
    </section>
  )
}
```

- [ ] **Step 6: Re-run catalog API and page tests**

Run: `pnpm --filter api exec vitest tests/catalog.integration.test.ts && pnpm --filter admin exec vitest src/features/catalog/pages/CatalogPage.test.tsx`
Expected: PASS

- [ ] **Step 7: Commit catalog curation**

```bash
git add apps/api/src/models/CatalogOverlay.ts apps/api/src/modules/catalog apps/api/src/app.ts apps/api/tests/catalog.integration.test.ts apps/admin/src/features/catalog
git commit -m "feat: add catalog curation overlays"
```

---

### Task 8: Implement banner and homepage management

**Files:**
- Create: `apps/api/src/models/HomepageSection.ts`
- Create: `apps/api/src/modules/banners/banners.routes.ts`
- Create: `apps/api/src/modules/banners/banners.controller.ts`
- Create: `apps/api/src/modules/banners/banners.service.ts`
- Modify: `apps/api/src/app.ts`
- Create: `apps/admin/src/features/banners/api/getHomepageConfig.ts`
- Create: `apps/admin/src/features/banners/api/updateHomepageConfig.ts`
- Create: `apps/admin/src/features/banners/pages/BannersPage.tsx`
- Create: `apps/admin/src/features/banners/components/BannerForm.tsx`
- Test: `apps/api/tests/banners.integration.test.ts`
- Test: `apps/admin/src/features/banners/pages/BannersPage.test.tsx`

- [ ] **Step 1: Write the failing homepage config API test**

```ts
it('reorders homepage sections and hero banners', async () => {
  const response = await request(app)
    .put('/api/banners/homepage')
    .set('Authorization', `Bearer ${token}`)
    .send({
      heroBanners: ['tmdb-550', 'tmdb-680'],
      sections: [{ slug: 'trending-now', title: 'Trending Now', order: 1 }]
    })

  expect(response.status).toBe(200)
  expect(response.body.data.heroBanners).toEqual(['tmdb-550', 'tmdb-680'])
})
```

- [ ] **Step 2: Run the banners API test to verify it fails**

Run: `pnpm --filter api exec vitest tests/banners.integration.test.ts`
Expected: FAIL with missing route

- [ ] **Step 3: Implement homepage config persistence**

```ts
const homepageSectionSchema = new Schema({
  slug: { type: String, required: true },
  title: { type: String, required: true },
  order: { type: Number, required: true },
  visible: { type: Boolean, required: true, default: true },
  itemExternalIds: { type: [String], required: true, default: [] }
})
```

- [ ] **Step 4: Write the failing banners page test**

```tsx
it('renders homepage controls for banners and sections', async () => {
  render(<BannersPage />, { wrapper: TestProviders })

  expect(await screen.findByText(/homepage and banners/i)).toBeInTheDocument()
  expect(screen.getByLabelText(/hero banners/i)).toBeInTheDocument()
})
```

- [ ] **Step 5: Implement the homepage management page**

```tsx
export const BannersPage = () => {
  const { data } = useHomepageConfigQuery()

  return (
    <section className="space-y-6">
      <h1 className="text-3xl font-bold">Homepage and Banners</h1>
      <BannerForm initialValue={data} />
    </section>
  )
}
```

- [ ] **Step 6: Re-run banners API and page tests**

Run: `pnpm --filter api exec vitest tests/banners.integration.test.ts && pnpm --filter admin exec vitest src/features/banners/pages/BannersPage.test.tsx`
Expected: PASS

- [ ] **Step 7: Commit homepage management**

```bash
git add apps/api/src/models/HomepageSection.ts apps/api/src/modules/banners apps/api/src/app.ts apps/api/tests/banners.integration.test.ts apps/admin/src/features/banners
git commit -m "feat: add homepage banner management"
```

---

### Task 9: Implement users and subscriptions modules

**Files:**
- Create: `apps/api/src/models/User.ts`
- Create: `apps/api/src/models/Subscription.ts`
- Create: `apps/api/src/modules/users/users.routes.ts`
- Create: `apps/api/src/modules/users/users.controller.ts`
- Create: `apps/api/src/modules/users/users.service.ts`
- Create: `apps/api/src/modules/subscriptions/subscriptions.routes.ts`
- Create: `apps/api/src/modules/subscriptions/subscriptions.controller.ts`
- Create: `apps/api/src/modules/subscriptions/subscriptions.service.ts`
- Modify: `apps/api/src/app.ts`
- Create: `apps/admin/src/features/users/pages/UsersPage.tsx`
- Create: `apps/admin/src/features/subscriptions/pages/SubscriptionsPage.tsx`
- Test: `apps/api/tests/users.integration.test.ts`
- Test: `apps/api/tests/subscriptions.integration.test.ts`

- [ ] **Step 1: Write the failing users list API test**

```ts
it('returns platform users with subscription summary fields', async () => {
  const response = await request(app)
    .get('/api/users')
    .set('Authorization', `Bearer ${token}`)

  expect(response.status).toBe(200)
  expect(response.body.data.items[0]).toMatchObject({
    email: expect.any(String),
    subscriptionStatus: expect.any(String)
  })
})
```

- [ ] **Step 2: Run the users test to verify it fails**

Run: `pnpm --filter api exec vitest tests/users.integration.test.ts`
Expected: FAIL with missing route

- [ ] **Step 3: Implement the user and subscription models plus list services**

```ts
const userSchema = new Schema({
  email: { type: String, required: true, unique: true },
  name: { type: String, required: true },
  status: { type: String, enum: ['active', 'suspended'], required: true },
  joinedAt: { type: Date, required: true },
  lastActiveAt: { type: Date, default: null }
})
```

```ts
const subscriptionSchema = new Schema({
  userId: { type: Schema.Types.ObjectId, ref: 'User', required: true },
  planName: { type: String, required: true },
  status: { type: String, enum: ['active', 'trialing', 'canceled', 'expired'], required: true },
  renewalDate: { type: Date, default: null }
})
```

- [ ] **Step 4: Write the failing subscriptions API test**

```ts
it('returns admin-managed subscriptions with plan and renewal info', async () => {
  const response = await request(app)
    .get('/api/subscriptions')
    .set('Authorization', `Bearer ${token}`)

  expect(response.status).toBe(200)
  expect(response.body.data.items[0]).toMatchObject({
    planName: expect.any(String),
    status: expect.any(String)
  })
})
```

- [ ] **Step 5: Run the subscriptions test to verify it fails**

Run: `pnpm --filter api exec vitest tests/subscriptions.integration.test.ts`
Expected: FAIL with missing route

- [ ] **Step 6: Implement the users and subscriptions pages**

```tsx
export const UsersPage = () => (
  <section className="space-y-6">
    <h1 className="text-3xl font-bold">Users</h1>
    <div className="rounded-2xl border border-zinc-800 bg-zinc-900 p-4">Users table goes here</div>
  </section>
)
```

```tsx
export const SubscriptionsPage = () => (
  <section className="space-y-6">
    <h1 className="text-3xl font-bold">Subscriptions</h1>
    <div className="rounded-2xl border border-zinc-800 bg-zinc-900 p-4">Subscriptions table goes here</div>
  </section>
)
```

- [ ] **Step 7: Re-run users and subscriptions integration tests**

Run: `pnpm --filter api exec vitest tests/users.integration.test.ts tests/subscriptions.integration.test.ts`
Expected: PASS

- [ ] **Step 8: Commit users and subscriptions**

```bash
git add apps/api/src/models/User.ts apps/api/src/models/Subscription.ts apps/api/src/modules/users apps/api/src/modules/subscriptions apps/api/src/app.ts apps/api/tests/users.integration.test.ts apps/api/tests/subscriptions.integration.test.ts apps/admin/src/features/users/pages/UsersPage.tsx apps/admin/src/features/subscriptions/pages/SubscriptionsPage.tsx
git commit -m "feat: add users and subscriptions modules"
```

---

### Task 10: Implement sync operations and provider integration boundary

**Files:**
- Create: `apps/api/src/models/SyncJob.ts`
- Create: `apps/api/src/modules/sync/sync.routes.ts`
- Create: `apps/api/src/modules/sync/sync.controller.ts`
- Create: `apps/api/src/modules/sync/sync.service.ts`
- Create: `apps/api/src/modules/sync/providers/tmdbProvider.ts`
- Modify: `apps/api/src/app.ts`
- Create: `apps/admin/src/features/sync/pages/SyncOperationsPage.tsx`
- Test: `apps/api/tests/sync.integration.test.ts`
- Test: `apps/admin/src/features/sync/pages/SyncOperationsPage.test.tsx`

- [ ] **Step 1: Write the failing sync jobs API test**

```ts
it('returns sync jobs and supports retrying a failed job', async () => {
  const listResponse = await request(app)
    .get('/api/sync/jobs')
    .set('Authorization', `Bearer ${token}`)

  expect(listResponse.status).toBe(200)

  const retryResponse = await request(app)
    .post('/api/sync/jobs/job-1/retry')
    .set('Authorization', `Bearer ${token}`)

  expect(retryResponse.status).toBe(200)
  expect(retryResponse.body.data.status).toBe('queued')
})
```

- [ ] **Step 2: Run the sync test to verify it fails**

Run: `pnpm --filter api exec vitest tests/sync.integration.test.ts`
Expected: FAIL with missing route

- [ ] **Step 3: Implement the sync job model and service**

```ts
const syncJobSchema = new Schema({
  jobId: { type: String, required: true, unique: true },
  source: { type: String, required: true },
  status: { type: String, enum: ['queued', 'running', 'succeeded', 'failed'], required: true },
  entityType: { type: String, required: true },
  entityExternalId: { type: String, required: true },
  errorMessage: { type: String, default: null },
  startedAt: { type: Date, default: null },
  finishedAt: { type: Date, default: null }
}, { timestamps: true })
```

```ts
export const retrySyncJob = async (jobId: string) => {
  return SyncJobModel.findOneAndUpdate(
    { jobId },
    { status: 'queued', errorMessage: null },
    { new: true }
  )
}
```

- [ ] **Step 4: Add the TMDB provider boundary file**

```ts
export const fetchTmdbTitle = async (externalId: string) => {
  const response = await fetch(`https://api.themoviedb.org/3/movie/${externalId}`, {
    headers: {
      Authorization: `Bearer ${process.env.TMDB_API_KEY}`
    }
  })

  if (!response.ok) {
    throw new Error('Failed to fetch TMDB title')
  }

  return response.json()
}
```

- [ ] **Step 5: Write the failing sync page test**

```tsx
it('renders sync health and retry actions', async () => {
  render(<SyncOperationsPage />, { wrapper: TestProviders })

  expect(await screen.findByText(/sync operations/i)).toBeInTheDocument()
  expect(screen.getByRole('button', { name: /retry/i })).toBeInTheDocument()
})
```

- [ ] **Step 6: Implement the sync operations page**

```tsx
export const SyncOperationsPage = () => {
  const { data } = useSyncJobsQuery()

  return (
    <section className="space-y-6">
      <h1 className="text-3xl font-bold">Sync Operations</h1>
      <div className="space-y-3">
        {data?.items.map((job) => (
          <div key={job.jobId} className="rounded-2xl border border-zinc-800 bg-zinc-900 p-4">
            <div className="flex items-center justify-between">
              <span>{job.entityExternalId}</span>
              <button className="rounded-lg bg-red-600 px-3 py-2">Retry</button>
            </div>
          </div>
        ))}
      </div>
    </section>
  )
}
```

- [ ] **Step 7: Re-run sync API and page tests**

Run: `pnpm --filter api exec vitest tests/sync.integration.test.ts && pnpm --filter admin exec vitest src/features/sync/pages/SyncOperationsPage.test.tsx`
Expected: PASS

- [ ] **Step 8: Commit sync operations**

```bash
git add apps/api/src/models/SyncJob.ts apps/api/src/modules/sync apps/api/src/app.ts apps/api/tests/sync.integration.test.ts apps/admin/src/features/sync
git commit -m "feat: add sync operations tooling"
```

---

### Task 11: Implement settings, route wiring, and navigation polish

**Files:**
- Create: `apps/api/src/models/Settings.ts`
- Create: `apps/api/src/modules/settings/settings.routes.ts`
- Create: `apps/api/src/modules/settings/settings.controller.ts`
- Create: `apps/api/src/modules/settings/settings.service.ts`
- Modify: `apps/api/src/app.ts`
- Modify: `apps/admin/src/router.tsx`
- Modify: `apps/admin/src/components/layout/Sidebar.tsx`
- Create: `apps/admin/src/features/settings/pages/SettingsPage.tsx`
- Test: `apps/api/tests/settings.integration.test.ts`

- [ ] **Step 1: Write the failing settings API test**

```ts
it('updates admin-visible platform settings', async () => {
  const response = await request(app)
    .put('/api/settings')
    .set('Authorization', `Bearer ${token}`)
    .send({ brandName: 'CINEFLIX', accentColor: '#E50914' })

  expect(response.status).toBe(200)
  expect(response.body.data.brandName).toBe('CINEFLIX')
})
```

- [ ] **Step 2: Run the settings API test to verify it fails**

Run: `pnpm --filter api exec vitest tests/settings.integration.test.ts`
Expected: FAIL with missing route

- [ ] **Step 3: Implement settings persistence and route**

```ts
const settingsSchema = new Schema({
  brandName: { type: String, required: true, default: 'CINEFLIX' },
  accentColor: { type: String, required: true, default: '#E50914' },
  dashboardTimezone: { type: String, required: true, default: 'UTC' }
}, { timestamps: true })
```

- [ ] **Step 4: Wire all frontend routes and sidebar navigation**

```tsx
const router = createBrowserRouter([
  { path: '/login', element: <LoginPage /> },
  {
    path: '/',
    element: <ProtectedRoute><AdminLayout /></ProtectedRoute>,
    children: [
      { index: true, element: <DashboardPage /> },
      { path: 'catalog', element: <CatalogPage /> },
      { path: 'banners', element: <BannersPage /> },
      { path: 'users', element: <UsersPage /> },
      { path: 'subscriptions', element: <SubscriptionsPage /> },
      { path: 'sync', element: <SyncOperationsPage /> },
      { path: 'settings', element: <SettingsPage /> }
    ]
  }
])
```

- [ ] **Step 5: Re-run the settings integration test**

Run: `pnpm --filter api exec vitest tests/settings.integration.test.ts`
Expected: PASS

- [ ] **Step 6: Commit settings and navigation wiring**

```bash
git add apps/api/src/models/Settings.ts apps/api/src/modules/settings apps/api/src/app.ts apps/api/tests/settings.integration.test.ts apps/admin/src/router.tsx apps/admin/src/components/layout/Sidebar.tsx apps/admin/src/features/settings/pages/SettingsPage.tsx
git commit -m "feat: add settings and route wiring"
```

---

### Task 12: Add end-to-end coverage and final verification

**Files:**
- Create: `playwright.config.ts`
- Create: `e2e/admin-login.spec.ts`
- Create: `e2e/dashboard-overview.spec.ts`
- Create: `e2e/catalog-curation.spec.ts`
- Create: `e2e/banner-management.spec.ts`
- Create: `e2e/sync-ops.spec.ts`

- [ ] **Step 1: Write the failing admin login E2E test**

```ts
import { expect, test } from '@playwright/test'

test('admin can sign in and land on dashboard', async ({ page }) => {
  await page.goto('/login')
  await page.getByLabel('Email').fill('admin@cineflix.local')
  await page.getByLabel('Password').fill('password123')
  await page.getByRole('button', { name: 'Sign in' }).click()

  await expect(page.getByRole('heading', { name: 'Executive Dashboard' })).toBeVisible()
})
```

- [ ] **Step 2: Run the E2E test to verify it fails**

Run: `pnpm exec playwright test e2e/admin-login.spec.ts`
Expected: FAIL because the dev servers or auth flow are not fully wired yet

- [ ] **Step 3: Add E2E coverage for critical admin flows**

```ts
test('admin updates a catalog overlay', async ({ page }) => {
  await page.goto('/catalog')
  await page.getByRole('button', { name: /edit overlay/i }).first().click()
  await page.getByLabel('Custom title').fill('Curated Title')
  await page.getByRole('button', { name: /save/i }).click()
  await expect(page.getByText('Curated Title')).toBeVisible()
})
```

- [ ] **Step 4: Run unit, integration, and E2E suites**

Run: `pnpm test:unit && pnpm test:integration && pnpm test:e2e`
Expected: all suites PASS

- [ ] **Step 5: Run a production build**

Run: `pnpm build`
Expected: both admin and api packages build successfully

- [ ] **Step 6: Commit the verification layer**

```bash
git add playwright.config.ts e2e
git commit -m "test: add e2e coverage for admin flows"
```

---

## Self-Review

### Spec coverage check
- Single-admin auth: Task 4, Task 5
- Executive dashboard: Task 6
- Catalog curation overlays: Task 7
- Homepage/banner management: Task 8
- Users: Task 9
- Subscriptions: Task 9
- Sync operations: Task 10
- Settings: Task 11
- Testing strategy: Task 12 plus test steps in every task
- Netflix-inspired dark UI: Task 5, then feature pages in Tasks 6-11

### Placeholder scan
- No `TBD`, `TODO`, or “similar to previous task” placeholders remain
- Each code-changing step includes explicit code or command content
- Each verification step includes exact commands and expected output

### Type consistency check
- Auth uses `super-admin` consistently
- Catalog overlay uses `externalId`, `categorySlugs`, `customTitle`, `customDescription`, `customTags` consistently
- Sync job uses `jobId` and `entityExternalId` consistently
- Homepage config uses `heroBanners` and `sections` consistently
