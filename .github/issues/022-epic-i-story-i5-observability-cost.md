---
title: "[Epic I] Story I5 — Observability & cost"
labels:
  - epic-i
  - observability
  - cost
  - storage
---

**Goal:** Know health and spend; catch corruption.

### Tasks
- [ ] Append audit logs on every write/read (audit container)
- [ ] Nightly integrity job (recompute SHA256 vs index)
- [ ] Metrics: bytes written/day, egress, failures
- [ ] Alerts on retry exhaustion/integrity mismatch
- [ ] Cost dashboard + lifecycle tuning
