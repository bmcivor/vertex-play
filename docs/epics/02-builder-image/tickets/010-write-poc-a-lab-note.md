## 10. Write POC-A lab note

**Epic:** E2 — Builder image

**As a** pipeline developer
**I want** POC-A's results captured in a structured lab note
**So that** decisions, versions, and failures are recorded for future epics to reference

### Context

Maps to T8 from [`builder-image.md`](../../../implementation/builder-image.md). This is the final ticket in E2.

### Acceptance criteria

- `docs/pocs/poc-a.md` exists and is committed
- Includes:
  - Header: POC-A, execution date, executor
  - Prerequisites: P1–P9 state (P1–P6 from E1 001, P7–P9 from 005)
  - Version pins: resolved G1–G8 values from 006
  - Artefacts: Dockerfile commit, Jenkinsfile commit, image tag + digest, `.aab` SHA256
  - Cross-repo changes: links to vertex-studio branch(es) from E1
  - Failures encountered and how they were resolved
  - Open questions to carry into POC-B (signing)

### Dependencies

- 009 (all POC-A execution is complete)

### Blocks

- E5 (signing work references POC-A findings and open questions)

### Priority

- Medium
