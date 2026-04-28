## 25. Write POC-C lab note

**Epic:** E6 — Play upload tooling

**As a** pipeline developer
**I want** POC-C documented as a lab note
**So that** E7 has a reference for how upload was wired up and what decisions were made

### Context

Mirrors E5 021 in structure. Lab note lives at `docs/pocs/poc-c.md`. Captures what worked, what versions/configs were used, and any failures and their resolutions.

### Acceptance criteria

- File `docs/pocs/poc-c.md` created
- Header: POC-C, execution date, executor
- GPP plugin version used
- Jenkins credential IDs used (not secret — just identifiers)
- Env var name used for service account JSON injection (`PLAY_SERVICE_ACCOUNT_JSON`)
- GPP task invoked and full task name
- Version code of the uploaded build
- Upload confirmation (Play Console internal track — AAB visible, description only)
- Failures encountered and how they were resolved
- Open questions for E7 (reference pipeline design, consumption model)
- Decision on whether the throwaway Jenkinsfile / job from 023 is deleted or kept as E7 reference

### Notes

- No service account JSON content, keystore passwords, or other credentials in the lab note
- OK to record the Play Console internal track URL (not a secret)

### Dependencies

- 024 (POC-C execution complete)

### Blocks

- E7 (clean baseline for reference pipeline work)

### Priority

- Medium
