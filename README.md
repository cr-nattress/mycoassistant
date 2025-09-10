# Myco Assistant

[![Sync Markdown Issues](https://github.com/cr-nattress/mycoassistant/actions/workflows/sync-issues.yml/badge.svg?branch=main)](https://github.com/cr-nattress/mycoassistant/actions/workflows/sync-issues.yml)

Production blueprint for an AI-assisted mushroom grow organizer. This repository describes the target end-state product and contains automation to manage the roadmap and delivery.

## What this product does (end-state)
Myco Assistant helps home and professional growers plan, track, and optimize mushroom grows with AI guidance.

- Landing site communicates value and funnels users into signup/login.
- App dashboard lists all grows as cards with filters and quick status.
- Step-by-step wizard captures species, substrate, environment, devices, and goals.
- On submission, an AI analysis produces a validated JSON plan with summary, risks, recommendations, targets, and versioning.
- Users post ongoing updates (photos/notes); re-analysis produces new versions and diffs.
- A shop module suggests prioritized products (rules + LLM rerank) with feedback signals.
- Portfolio and insights views surface trends, anomalies, and cohort comparisons across grows.

## Core capabilities
- AI analysis pipeline with schema validation (Ajv) and versioned outputs.
- Secure data storage for prompts/responses/analyses with integrity checks.
- Per-grow timeline, version badges, and re-analysis flow.
- Shop recommendations driven by analysis outcomes and user feedback.
- Organization-wide insights: highlights, KPIs, anomalies, cohorts, simple experiments.

## Architecture (planned)
Monorepo managed with pnpm workspaces + Turbo.

- apps/ (Next.js 14 App Router, React 18)
  - `apps/landing`: marketing site
  - `apps/organizer`: product UI
- services/ (Express on Netlify Functions)
  - `services/api`: grow CRUD and analysis trigger endpoints
  - `services/email`: outbound email queue
  - `services/media`: upload signing and media helpers
- packages/
  - `packages/config`: shared ESLint/Prettier/tsconfig
  - `packages/shared`: types, utilities, Zod env loaders
  - `packages/ui`: design tokens and shared UI components
- tests/
  - `tests/e2e-ui`: Playwright UI flows
  - `tests/e2e-api`: API health and CRUD flows
- docs/
  - Docusaurus site (guides, ADRs, TypeDoc reference)
- .github/
  - Workflows and file-based issues that sync to GitHub Issues

See `plans/technology.md` and `plans/setup-guide.md` for the full implementation plan.

## Data, AI, and storage
- Prompts and raw responses stored as blobs; validated analyses written separately.
- Azure Blob Storage foundation with containers: `prompts-raw`, `responses-raw`, `analyses-valid`, `audit`.
- SDK helpers implement retry/backoff, gzip, and SHA256 hashing.
- Governance: PII scrubbing, lifecycle rules for raw vs. validated assets, export and right-to-delete.
- Observability and cost controls: audit logs, nightly integrity job, metrics and alerts.

## Security & compliance
- Strict security headers, CORS allowlist, and server/client env validation via Zod.
- Error boundaries, toast surfaces, and structured logging with request IDs.
- Accessibility baseline: WCAG AA, focus-visible, prefers-reduced-motion.

## CI/CD
- GitHub Actions build and test pipeline for apps/services/docs/storybook.
- Deploy previews for apps/services (Netlify), optional Vercel as needed.
- Issue automation: `.github/issues/*.md` are synced to GitHub Issues via `.github/workflows/sync-issues.yml`.

## Roadmap (epics)
Mapped from `plans/issues.csv` and `.github/issues/`.

- Epic A: Landing + Auth
  - A1 Landing value + signup/login
  - A2 Login/signup flow
- Epic B: Dashboard
  - B1 Grow cards with filters
  - B2 Add new grow CTA
- Epic C: Grow setup
  - C1 Wizard and autosave
  - C2 Advanced options
  - C3 Goals
- Epic D: Initial AI Analysis
- Epic E: Grow detail
  - E1 View analysis with version badge
  - E2 Add update + re-analysis
- Epic F: Shop recommendations
- Epic G: Insights
  - G1 Portfolio overview
  - G2 Per-grow deep dive
  - G3 Cohorts & experiments
- Epic H: Infra & UX System
  - H1 Data & Files
  - H2 Validation, Errors, Observability
  - H3 UX System
- Epic I: Storage Vault & Governance
  - I1 Azure Blob foundation
  - I2 Write & index flow
  - I3 Governance (PII, lifecycle, deletion)
  - I4 Vault Viewer (admin & per-user)
  - I5 Observability & cost

## Developer experience
- Monorepo scripts: build, dev, lint, typecheck, test, e2e, docs, storybook.
- Shared config and tokens; Storybook for UI; TypeDoc for API docs.
- Backlog-as-code: file-based epics → stories → tasks under `.github/issues/` and `plans/`.
- Prompts for agents under `prompts/` to ensure consistent long-term context and rule following.

## Getting started (outline)
1) Clone and install: pnpm 9+, Node 20+.
2) Copy `.env.example` to `.env` and fill values.
3) Run dev: `pnpm dev` (Turbo will orchestrate tasks).
4) Run tests: `pnpm test` and E2E with `pnpm e2e`.
5) Build: `pnpm build`. Docs/Storybook via `pnpm docs` and `pnpm -w storybook`.

## Repository structure (current)
- `.github/issues/` — Markdown issues (YAML frontmatter)
- `.github/workflows/sync-issues.yml` — Issues sync workflow
- `.github/ISSUE_TEMPLATE/` and `.github/PULL_REQUEST_TEMPLATE.md`
- `plans/issues.csv` — Source backlog
- `plans/technology.md` — Architecture and stack
- `plans/setup-guide.md` — Step-by-step setup
- `prompts/` — Long-term context and rule-following prompts

## License
TBD
