---
title: "[Epic I] Story I3 — Governance (PII, lifecycle, deletion)"
labels:
  - epic-i
  - compliance
  - privacy
  - storage
---

**Goal:** Keep data compliant, lean, and erasable on request.

### Tasks
- [ ] PII scrubbing for prompts (strip emails/names if present)
- [ ] Lifecycle rules: expire raw after 30/90d; keep analyses
- [ ] User data export (ZIP of blobs by userId)
- [ ] Right-to-delete: delete blobs + index rows
- [ ] Immutability (audit container) optional toggle
