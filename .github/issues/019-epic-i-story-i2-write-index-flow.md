---
title: "[Epic I] Story I2 — Write & index flow"
labels:
  - epic-i
  - analysis
  - storage
---

**Goal:** On every analysis call, write blobs and DB index.

### Tasks
- [ ] Compose blob keys: user/{userId}/grow/{growId}/analysis/{analysisId}/{version}/{ts}-{kind}.json.gz
- [ ] Write prompt & raw response to raw containers
- [ ] Validate with Ajv; write validated JSON to analyses-valid
- [ ] DB index row (URIs, sizes, hashes, version)
- [ ] Unit tests (happy path + retry)
