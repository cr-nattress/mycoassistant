---
title: "[Epic I] Story I1 — Azure Blob foundation"
labels:
  - epic-i
  - infra
  - storage
  - azure
---

**Goal:** Stand up secure blob storage for prompts/responses/analyses.

### Tasks
- [ ] Provision storage account + resource group
- [ ] Create containers: prompts-raw, responses-raw, analyses-valid, audit
- [ ] App registration + role assignment; rotateable credentials
- [ ] Env wiring: AZURE_STORAGE_ACCOUNT, AZURE_CLIENT_ID/SECRET/TENANT
- [ ] SDK helper with retry/backoff, gzip, SHA256 hashing
