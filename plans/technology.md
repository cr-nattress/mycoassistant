High-level architecture

Monorepo managed with pnpm workspaces + Turbo for task graph/caching; layout includes apps/ (Next.js), services/ (Express on Netlify Functions), shared packages/, E2E tests/, docs in /docs and a Docusaurus site in /website, global Storybook, DX scripts, backlog, and GitHub workflows.

Two web apps (Next.js App Router): apps/landing (marketing site) and apps/organizer (app UI). Both TypeScript, strict mode, Supabase client, and Netlify config.

Three serverless services (Express): services/api, services/email, services/media. Express wrapped with serverless-http, per-service netlify.toml, versioned routes (/v1/*), and Supabase server client.

Core technologies & frameworks

Package / Build / Tasks: pnpm workspaces, Turbo, TypeScript (strict).

Web framework: Next.js 14 (App Router), React 18.

APIs: Express + serverless-http deployed as Netlify Functions; Zod for validation.

Auth/data: Supabase JS client for both apps and services (public anon for apps, service role for APIs).

Docs: Docusaurus site serving MDX from /docs; TypeDoc emits Markdown API refs to /docs/reference.

Component docs / design system: Storybook (Next.js framework + Vite builder) reading stories from shared UI and app components.

Testing: Playwright E2E (UI + API workspaces), Vitest for unit tests (services and apps).

CI/CD & repo hygiene: GitHub Actions (build Storybook, TypeDoc, Docs), Husky + lint-staged + Commitlint; optional Vercel previews.

Env & security: .env example, CORS policy in services, security headers for apps, preview wiring notes for Netlify/Vercel.

Backlog system: file-based epics → user stories → tasks; MADR style; prompt templates for agent tasks.

Contracts: OpenAPI placeholder for services/api; repo script placeholder to validate (future swagger).

Monorepo structure & shared packages

Directory layout: apps/, services/, packages/{config,shared,ui}, tests/{e2e-ui,e2e-api}, docs, website, .storybook, scripts, backlog, .github. Includes turbo.json, pnpm-workspace.yaml, and root scripts for dev/build/lint/typecheck/docs/storybook.

packages/config: shared tsconfig.base.json, eslint.config.js, prettier.config.cjs.

packages/shared: shared types + zod-based env validators (validateClientEnv, validateServerEnv).

packages/ui: shared UI library with an example component and Storybook story; exported via src/index.ts.

Applications (Next.js)

apps/landing (teamhunt.pro): Next 14, React 18, strict mode, Supabase client (NEXT_PUBLIC_…), netlify.toml (Next plugin), minimal homepage linking to organizer app.

apps/organizer (app.teamhunt.pro): same stack; starter page “Organizer Dashboard”.

Services (serverless APIs)

Common: Express app with express.json(), /v1/healthz, Supabase server client (SUPABASE_URL, SUPABASE_SERVICE_ROLE_KEY), esbuild bundling via Netlify config, redirects mapping /v1/* to function handler.

api service routes: GET /v1/orgs (demo data + example query), POST /v1/events (insert example).

email service: POST /v1/send (queue email).

media service: POST /v1/uploads/sign (signed upload URL stub).

Documentation stack

Docusaurus app at /website: navbar links (Docs, Storybook), reads from /docs, optional Algolia search via env, sidebars for guides/reference/ADRs. TypeDoc outputs to /docs/reference, built via root scripts api:build, docs:build.

ADRs: MADR style; seed ADR tracks docs stack decision.

Testing strategy

E2E UI: Playwright with env-driven baseURL (defaults for landing/app). Smoke test navigates landing → organizer.

E2E API: Playwright hitting api/email/media base URLs; health checks and event POST path.

Unit tests: Vitest in services/api (healthz + events spec), Vitest + Testing Library + jsdom for organizer app.

CI/CD, quality & automation

GitHub Actions CI: install via pnpm, build Storybook, TypeDoc, Docusaurus; upload artifacts; optional Vercel preview stage.

Hooks: Husky pre-commit runs lint-staged; lint-staged formats with Prettier & fixes with ESLint; Commitlint enforces conventional commits.

Turbo: pipeline defines build outputs for Next/Storybook/website/docs; dev un-cached; project-wide lint, typecheck, test.

Env, security, previews

.env.example: app and services base URLs; Supabase (public + service); optional Algolia; legacy DATABASE_URL.

Security headers (per app) via _headers: X-Frame-Options DENY, nosniff, strict referrer policy, locked permissions-policy.

CORS: services allow localhost app, production app domain, and deploy-preview wildcard (commented for later tightening).

Previews: Netlify config present for apps/services; notes for optional Vercel previews in README/CI.

Contracts & API docs

OpenAPI placeholder for services/api (healthz, events), plus a root script placeholder to validate existence and to evolve to swagger tooling later.

Repo guidelines & DX

Scripts: root scripts to run per-package dev (dev:*), build all, lint/format/typecheck/test, clean (turbo clean && git clean -fdX), docs build/serve, Storybook dev/build, TypeDoc build, and DX scaffolder (scripts/new-component.mjs).

Backlog as code: opinionated structure with templates for epic/story/task, plus agent-oriented task prompts.

Storybook setup: Next.js framework, Vite builder, docs/test addons; reads stories from shared UI and app components.

TL;DR (stack capsule)

Monorepo: pnpm + Turbo + strict TS; shared config/env/types/UI.

Frontend: Next.js 14, React 18, Supabase client, Netlify deploy.

Backend: Express on Netlify Functions via serverless-http, Zod, Supabase service key.

Docs/Design System: Docusaurus + TypeDoc + Storybook (Vite).

Testing: Playwright E2E (apps/services), Vitest unit tests.

CI/Quality: GH Actions builds docs/storybook; Husky, lint-staged, Commitlint.

Security/Env: locked headers, CORS allowlist, env templates; preview notes for Netlify/Vercel.