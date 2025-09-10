New Product Monorepo Setup Guide
This document is the single source of truth for standing up a production-grade web product with the same organization and technology stack as TeamHunt. It is optimized for coding agents and humans to execute in order.

Use this README to:

Scaffold a pnpm-based monorepo with Next.js apps, Netlify functions, shared packages, and docs.
Establish quality gates (lint, tests, CI, preview deploys).
Generate epics and user stories aligned with the initial roadmap.
Decide what to implement first.
0) High-Level Goals
Fast, accessible, mobile-first UX.
Clear domain separation: marketing site, product app(s), backend services, shared packages.
Strong developer ergonomics: type-safe env, codegen-ready, CI/CD, previews.
Documentation and design system from Day 1.
1) Tech Stack
Monorepo: pnpm workspaces + Turbo repo tasks
Frontend: Next.js 14 (App Router), React 18
UI: Shared component library (packages/ui/), design tokens
Shared types/utilities: packages/shared/ (Zod-based env, shared interfaces)
Docs: Docusaurus 3 + Storybook + TypeDoc
Tests: Playwright (E2E), Vitest (unit)
Back end: Netlify Functions (per service: API, email, media)
CI/CD: GitHub Actions + Netlify deploy previews (Vercel optional)
Code quality: ESLint, Prettier, commitlint, lint-staged, Husky
ADRs: Architecture decision records under docs/adr/
2) Repository Structure
Create the following folders and baseline files:

apps/
landing/ (Next.js marketing)
app/ or organizer/ (Next.js product app)
services/
api/ (Netlify functions)
email/ (Netlify functions)
media/ (Netlify functions)
packages/
config/ (eslint, prettier, tsconfig exports)
shared/ (zod env + shared types)
ui/ (design tokens + React UI components)
website/ (Docusaurus docs)
tests/
e2e-ui/ (Playwright)
e2e-api/ (Playwright or API tests)
docs/adr/ (architecture decisions)
scripts/ (scaffolding scripts)
Root configs: .husky/, .github/workflows/, 
turbo.json
, 
pnpm-workspace.yaml
, 
package.json
, 
typedoc.json
, 
commitlint.config.cjs
, 
lint-staged.config.mjs
3) Prerequisites
Node 20+, pnpm 9+
GitHub repo with Actions enabled
Netlify account (one site per app/service, custom domains)
Optional: Vercel for secondary previews
4) Bootstrap Monorepo
Root files:

json
// package.json
{
  "name": "@org/monorepo",
  "private": true,
  "packageManager": "pnpm@9",
  "scripts": {
    "build": "turbo run build",
    "dev": "turbo run dev --parallel",
    "lint": "turbo run lint",
    "typecheck": "turbo run typecheck",
    "format": "prettier --write .",
    "test": "turbo run test",
    "e2e": "turbo run e2e",
    "docs": "pnpm --filter @org/website build"
  },
  "devDependencies": {
    "turbo": "^2.0.0",
    "prettier": "^3.3.3",
    "eslint": "^9.10.0",
    "@commitlint/cli": "^19.5.0",
    "@commitlint/config-conventional": "^19.5.0",
    "lint-staged": "^15.2.9",
    "husky": "^9.1.4",
    "typescript": "^5.6.2"
  }
}
yaml
# pnpm-workspace.yaml
packages:
  - "apps/*"
  - "services/*"
  - "packages/*"
  - "website"
  - "tests/*"
json
// turbo.json
{
  "$schema": "https://turbo.build/schema.json",
  "pipeline": {
    "build": { "dependsOn": ["^build"], "outputs": [".next/**", "dist/**"] },
    "dev": {},
    "lint": {},
    "typecheck": {},
    "test": {},
    "e2e": { "dependsOn": ["build"] }
  }
}
Husky + commitlint:

bash
# Setup (run once)
pnpm dlx husky-init && pnpm install
sh
# .husky/pre-commit
pnpm lint-staged
js
// commitlint.config.cjs
module.exports = { extends: ["@commitlint/config-conventional"] };
js
// lint-staged.config.mjs
export default {
  "*.{ts,tsx,js,jsx,md,mdx,css}": ["prettier --write"]
};
5) Environment
Create 
.env.example
 and keep it exhaustive.

env
# Application URLs (used by E2E tests and development)
APP_BASE_URL=http://localhost:3001
LANDING_BASE_URL=http://localhost:3000
API_BASE_URL=http://localhost:8888

# Auth/DB (e.g., Supabase)
SUPABASE_URL=
SUPABASE_ANON_KEY=

# Service URLs (prod)
APP_ORIGIN=
LANDING_ORIGIN=
API_ORIGIN=
EMAIL_ORIGIN=
MEDIA_ORIGIN=

# JWT / Secrets
JWT_SECRET=
In packages/shared/src/env.ts define a Zod schema that splits server vs client variables and throws on invalid.

6) Packages
packages/config/
eslint.config.js exporting a base config for TS/React/Next
prettier.config.cjs
tsconfig.base.json with strict settings (ESNext, moduleResolution NodeNext)
packages/shared/
src/env.ts (Zod env loader for server, client-safe proxy)
src/types/ interfaces and shared enums
src/index.ts exports
packages/ui/
src/tokens.css (CSS variables: colors, spacing, radius, shadows, motion, typography)
src/tokens.ts (export token values for TS consumers)
src/components/ minimal set: Button, Header, Card, Input, Modal
Build with tsc for now; later add Storybook stories.
7) Apps
Each Next.js app should be minimal and import tokens + shared UI.

apps/landing/ (Next 14)
app/ with layout.tsx, page.tsx, globals.css
Bring in @org/ui/src/tokens.css via globals.css
Netlify: 
netlify.toml
 with Next plugin, Node 20, pnpm, headers, caching
apps/app/ or apps/organizer/ (Next 14)
App router with authenticated sections scaffolded later
Import tokens and shared UI
Early route: /status (health), /demo (map or feature demo)
Example Netlify config:

toml
# apps/landing/netlify.toml
[build]
  command = "pnpm -F @org/landing build"
  publish = ".next"

[[plugins]]
  package = "@netlify/plugin-nextjs"

[build.environment]
  NODE_VERSION = "20"
  PNPM_VERSION = "9"
  NETLIFY_USE_PNPM = "true"

[[headers]]
  for = "/*"
  [headers.values]
    X-Frame-Options = "DENY"
    X-XSS-Protection = "1; mode=block"
    X-Content-Type-Options = "nosniff"
    Referrer-Policy = "strict-origin-when-cross-origin"
    Permissions-Policy = "camera=(), microphone=(), geolocation=()"

[[headers]]
  for = "/_next/static/*"
  [headers.values]
    Cache-Control = "public, max-age=31536000, immutable"
8) Services (Netlify Functions)
Create services/api/, services/email/, services/media/:

Each has netlify/functions/ with function entrypoints.
netlify.toml
 per service with build command and Node 20.
Add CORS middleware allowing localhost + production domains.
Keep each service deployable independently in Netlify with custom subdomains.
9) Documentation & Design System
Docusaurus in website/ for product docs:
Quickstart, API reference, ADR index
Storybook for packages/ui/:
Stories for core components; visual regression later
TypeDoc for generating reference docs from TS source
10) Testing
Unit: Vitest per package/app with tsconfig support
E2E: Playwright projects:
tests/e2e-ui/ for landing and app smoke flows
tests/e2e-api/ for API endpoints
Configure CI to run unit + e2e and attach artifacts (screenshots, videos)
11) CI/CD (GitHub Actions)
Workflow: on PR
Install deps with pnpm cache
Lint + typecheck + unit tests
Build all apps and services
Trigger Netlify deploy previews for apps/* and services/*
Upload Playwright artifacts
Workflow: on main
Build + deploy to production Netlify sites
Secrets required:

Netlify auth tokens (if using API), otherwise configure in Netlify UI
Optional Vercel: VERCEL_TOKEN, VERCEL_ORG_ID, VERCEL_PROJECT_ID
12) Security & Compliance
Security headers via Netlify [[headers]]
CORS locked down to known origins
Do not log secrets; scrub PII
Rate limiting and input validation at API edge (Zod validation, schema-first)
Accessibility: WCAG AA, prefers-reduced-motion, :focus-visible
13) Performance
Next: use next/image, font optimization via next/font
Ship minimal JS to landing pages; avoid heavy client code
Cache static assets aggressively (immutable)
Monitor CLS/LCP with Lighthouse thresholds (90+ mobile)
14) Scaffolding Scripts
scripts/new-component.mjs: scaffold a component in packages/ui/src/components/<Name>/ with test, story, and README.
scripts/new-service.mjs: scaffold Netlify function with boilerplate handlers and CORS.
15) First Implementation Order (What to Build First)
Milestone 0: Foundation
Monorepo skeleton, pnpm workspaces, turbo, root scripts
packages/config, packages/shared (env + types), packages/ui tokens + Button
apps/landing minimal page using tokens, deploy to Netlify preview
website bootstrapped with a “Getting Started” doc
CI: lint, typecheck, build
Milestone 1: UX & Docs
packages/ui: Header, Card, Input, Modal with accessibility
Storybook for UI; Docusaurus docs for setup and contributing
E2E UI smoke tests for landing pages
Security headers and CORS defaults in place
Milestone 2: Services & App
services/api first endpoints (health, version)
apps/app or apps/organizer basic routes + auth placeholder
E2E API tests; wire deploy previews for services
TypeDoc generation into docs/reference/
16) Epics and Sample User Stories
Epics:

Design System & Tokens
Marketing Site (Landing)
Product App Shell
Backend Services (API/Email/Media)
Documentation Platform
CI/CD & Quality Gates
Security & Compliance
Observability & Analytics (optional)
User Stories (ready to drop into issues):

As a visitor, I can load the landing page fast on mobile and see a primary CTA above the fold.
As a developer, I can import a Button from @org/ui and get token-compliant styles by default.
As a screen-reader user, I can navigate all interactive elements with visible focus.
As an operator, I can see a health endpoint in services/api that returns version and status 200.
As a PM, I can read “Getting Started” docs and run the app locally in under 10 minutes.
As a developer, my PR automatically builds, runs tests, and creates preview deployments for each app/service.
Acceptance Criteria examples:

Lighthouse mobile scores ≥ 90 for Performance, Accessibility, Best Practices, SEO on landing.
E2E smoke passes on PR for landing and app shell.
All UI components meet WCAG AA contrast and keyboard nav; :focus-visible implemented.
Netlify deploy previews available for every PR in apps/* and services/*.
17) Contribution Workflow
Conventional commits enforced via commitlint
Pre-commit: lint-staged formats changed files
PR checks must pass: lint, typecheck, unit, build, e2e (smoke)
ADRs for architectural changes (docs/adr/)
18) LLM Prompts (Ready to Copy)
“Scaffold a Next.js 14 app in apps/landing with App Router, import @org/ui/src/tokens.css in globals.css, create a hero section using Button from @org/ui, and add a Netlify 
netlify.toml
 with Next plugin.”
“Create packages/ui/src/components/Button.tsx with primary/secondary/link variants, :focus-visible styles, and tokens for color/spacing/typography. Include a Storybook story and a Vitest test.”
“Set up packages/shared/src/env.ts with Zod schema separating server-only and client-safe variables. Export a typed 
env
 object and throw on invalid.”
“Create a GitHub Actions workflow that runs lint, typecheck, unit, build, Playwright smoke, and publishes artifacts; also trigger Netlify preview deploys for any apps/* or services/* changed.”
19) Deployment
Netlify site per app/service:
Link each subfolder as a separate site
Configure environment variables from 
.env.example
Set custom domains per product area
Optional Vercel previews for marketing or docs
20) Definition of Done (for each milestone)
Code: typed, linted, formatted; tokens applied; accessibility checked
Tests: unit + e2e smoke passing
CI/CD: green; preview deploy accessible
Docs: updated (Docusaurus), ADR added if architecture changed
Security: headers, CORS, no secrets in logs