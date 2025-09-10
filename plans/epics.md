EPIC A — Landing & Auth (mobile-first marketing + entry)

Goal: Sell the app, drive signup/login, and route users into the dashboard.

A1 — As a visitor, I understand the value and can sign up or log in

Acceptance Criteria

Landing has hero, features, testimonials, pricing, footer.

Prominent CTAs: Create Account / Log in (header + hero).

Fully responsive; lighthouse perf ≥ 90 mobile.

Tasks

 Build app/(public)/page.tsx (hero, sections, footer)

 Add sticky header with logo + CTAs

 Configure auth routes (app/(auth)/login, /signup)

 Hook auth provider (next-auth or custom) with email/pass baseline

 Analytics events for CTA clicks

 Add basic SEO/meta (OG tags, favicon)

A2 — As a user, I can sign up / log in on mobile easily

Acceptance Criteria

Email/password flow, error handling, forgot password (stub OK).

Redirect to Dashboard on success.

Tasks

 Forms with zod validation

 Auth session guard (middleware)

 Success/error toasts; skeleton on redirect

EPIC B — Dashboard (All Grows)

Goal: Show all grows as easy-to-scan cards; add new grow quickly.

B1 — As a user, I see my grows as cards and can filter

Acceptance Criteria

Card shows: cover image/icon, name, status, quick stats.

Empty state for first-time users.

Mobile: vertical stack; Desktop: grid.

Tasks

 app/(app)/dashboard/page.tsx with GrowCard component

 Store & list grows (Prisma models: User, Grow)

 Filters: Active / Completed / Archived (client state)

 “Shop for this grow” action on card (link to Shop with growId)

B2 — As a user, I can add a new grow from the dashboard

Acceptance Criteria

Floating + New Grow button visible on mobile.

Tasks

 Floating CTA + route to wizard (/grows/new)

 Nice micro-animations (framer-motion)

EPIC C — Setup New Grow (Typeform-style Wizard)

Goal: Guided, conversational flow with Goals and Advanced Options.

C1 — As a user, I can complete a focused step-by-step setup

Acceptance Criteria

Steps: Name → Species → Substrate → Environment → Devices → Dashboard Image → Initial Image → Goals → Advanced → Review.

Progress indicator, autosave per step.

Tasks

 Wizard shell with one-question-per-screen pattern

 Step forms & zod schemas per step

 Local draft state + server draft save on blur/next

 File uploads via pre-signed URLs (S3/Cloud Storage helper)

 Review screen with edit links

C2 — As a power user, I can configure advanced options

Acceptance Criteria

Collapsible Advanced: external data feeds (API/CSV), extra images, IoT device placeholders, metadata.

Tasks

 Advanced panel UI & schema

 External links/ref image inputs (Drive/Dropbox/S3 URL fields)

 Device config placeholders (AC Infinity/API key fields, stored but not yet used)

C3 — As a user, I set Goals for timeline, yield, flushes

Acceptance Criteria

Inputs for target harvest date or cycle length, yield goal (g), expected flush count, custom goals.

Goals appear as chips on detail page.

Tasks

 Goal model fields + UI (sliders/steppers/date pickers)

 Persist + reflect in Review

EPIC D — AI Initial Analysis (on Submit)

Goal: On submit, send structured prompt + images to AI, get strict JSON analysis, and display it.

D1 — As a user, I get a clear, actionable initial plan

Acceptance Criteria

Submit button: Save & Analyze.

Analysis returns JSON (schema-validated) with summary, target ranges, risks, recommendations, goal alignment, checklist, version tag (e.g., v1-initial).

Error states are handled (retry once, show message if invalid).

Tasks

 /api/grows (create) and /api/grows/[id]/analyze (POST)

 Compose System/User prompts; temperature 0.2

 Ajv schema + validator; retry on invalid JSON

 Persist prompts/responses for audit

 Success screen with confetti + deep link to Grow Detail

EPIC E — Grow Detail & Re-Analysis

Goal: Show analysis cards and a timeline; allow updates with re-analysis.

E1 — As a user, I can view my grow’s current plan & targets

Acceptance Criteria

Cards: Summary, Target Ranges, Risks, Recommendations (Now/Soon/Later), Goal Alignment.

Show analysis version; expandable “Why this?” rationales.

Tasks

 app/grows/[id]/page.tsx with analysis card components

 Badge for version (v1, v2-2025-09-09)

E2 — As a user, I can add updates (images + notes) and re-analyze

Acceptance Criteria

“+ Add Update” → upload photo(s), text note, toggle Run AI Re-analysis.

New analysis appended to versioned timeline with diff vs. previous.

Tasks

 /api/grows/[id]/reanalyze (delta prompt including last analysis)

 Timeline model & UI (entries, version badges)

 Diff visualization (“Changed targets”, “Added recs”)

EPIC F — AI-Guided Shop / Helpful Links

Goal: Recommend supplies to close gaps from analyses, with rationale.

F1 — As a user, I see prioritized product suggestions per grow

Acceptance Criteria

Sections: Now, Soon, Later.

Each card shows vendor link, brief rationale tied to analysis evidence.

Filter by grow (chips) and category.

Tasks

 Product catalog (/data/products.json + Prisma optional)

 /api/shop/products, /api/shop/recommendations?growId=...

 Rules matcher (deterministic) + optional LLM re-ranker

 app/shop pages + ProductCard, InsightBar

 Feedback endpoint: owned/hidden/clicked, use to downrank

EPIC G — Long-Term Analysis (Insights)

Goal: Spot trends per grow and across all grows; surface evidence-linked insights.

G1 — As a user, I get an overview of what’s working across my grows

Acceptance Criteria

Highlights bar (AI): top improvements/regressions with evidence (metric deltas).

KPIs: Yield, BE, Time-to-Harvest, Stability %, Contam rate.

Tasks

 Metric ingestion (DailySnapshot model; aggregates)

 /api/insights/overview?period=90d

 Sparkline/scorecard components

G2 — As a user, I deep-dive an individual grow’s history

Acceptance Criteria

Timelines for yield/flushes, stability gauges, anomalies, change log.

Tasks

 /api/insights/grow/[id]?period=...

 StabilityGauge, AnomalyList, ChangeLog comps

G3 — As a user, I can compare cohorts and design simple experiments

Acceptance Criteria

Cohort tiles (species/substrate/environment) with medians/variance.

Define A/B experiments; show uplift with simple CI.

Tasks

 Cohort definition & filter engine

 /api/insights/cohorts, /api/insights/experiments

 Simple bootstrap/t-test util; labels for confidence

EPIC H — Platform & Infrastructure

Goal: Foundational plumbing, performance, and safety.

H1 — Data & Files

Tasks

 Prisma schema (User, Grow, Analysis, TimelineEntry, DailySnapshot, Product, Feedback)

 S3/Blob helper with pre-signed upload; store URLs only

 Migrations + seed scripts (demo grows/products)

H2 — Validation, Errors, Observability

Tasks

 Central zod/ajv validators

 Error boundary + user-friendly toasts

 Basic logging; request IDs on AI calls

H3 — UX System

Tasks

 Tailwind + shadcn/ui baseline; tokens for spacing/typography

 framer-motion transitions (wizard, cards, chips)

 Accessibility pass (labels, focus rings, reduced-motion)

Milestones & Dependencies

M1 — Skeleton & Auth: Epics A, H3 basics.

M2 — Dashboard + Wizard Core: Epics B, C1/C3.

M3 — AI Initial Analysis: Epic D + E1 display.

M4 — Re-Analysis & Timeline: Epic E2.

M5 — Shop (Rules Engine): Epic F1 minimal.

M6 — Insights (Overview + Per-Grow): G1–G2.

M7 — Cohorts & Experiments: G3.

M8 — Polish & Perf: H1/H2 refinements, SEO, accessibility.

Definition of Done (global)

Mobile lighthouse ≥ 90 (perf/accessibility/best practices).

All API inputs validated; AI outputs schema-validated.

No blocking console errors; error states handled.

Empty states, loading states, and success states present.

Basic unit tests for services (analysis schema validate, rules matcher, aggregations).