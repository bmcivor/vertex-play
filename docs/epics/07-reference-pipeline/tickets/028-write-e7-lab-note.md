## 28. Write E7 lab note

**Epic:** E7 — Reference app pipeline

**As a** pipeline developer
**I want** E7 documented as a lab note
**So that** E8 has a clean baseline and the reference pipeline's design decisions are on record

### Context

Unlike prior POC lab notes (A/B/C), this note is less about "did the POC work" and more about documenting the reference pipeline's final shape — decisions made, anything that diverged from the spec, and what an app developer needs to know to adopt it.

Lab note lives at `docs/pocs/e7-reference-pipeline.md`.

### Acceptance criteria

- File `docs/pocs/e7-reference-pipeline.md` created
- Header: E7, completion date, executor
- Reference Jenkinsfile location (`Jenkinsfile` at repo root) and how to copy-and-adapt it (per ADR-006)
- `versionCode` derivation: `BUILD_NUMBER`, where it's read in `build.gradle.kts`
- Branch trigger summary: what runs on PR / main / tag — confirmed by 027
- Jenkins credential IDs required (not the secrets themselves — just the IDs an app developer needs to replicate)
- Any divergence from `spec.md` noted
- Failures encountered during 027 validation and how they were resolved
- Open questions or known limitations for E8 to address

### Dependencies

- 027 (validation complete)

### Blocks

- E8 (handoff doc references this note)

### Priority

- Medium
