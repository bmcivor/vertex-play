## 21. Write POC-B lab note

**Epic:** E5 — Signing

**As a** pipeline developer
**I want** POC-B documented as a lab note
**So that** E6 and E7 have a reference for how signing was wired up and what decisions were made

### Context

Mirrors E2 010 in structure. Lab note lives at `docs/pocs/poc-b.md`. Captures what worked, what versions/configs were used, and any failures and their resolutions — without leaking secrets.

### Acceptance criteria

- File `docs/pocs/poc-b.md` created
- Header: POC-B, execution date, executor
- Keystore metadata (no secret values):
  - Algorithm, key size, validity period
  - Alias
  - Subject DN (if non-identifying)
  - Backup location (description only, not an actual path)
- Jenkins credential IDs used (these are not secret — they're just identifiers)
- Env var names used in Gradle signing config
- Signed AAB SHA256
- `jarsigner -verify` output summary
- Play App Signing state confirmed (auto-enrolled via 015 — any surprises?)
- Failures encountered and how they were resolved
- Open questions for POC-C (upload)
- Decision on whether the throwaway pipeline job from 020 is deleted or kept as E7 reference

### Notes

- No passwords, path-to-keystore, or certificate private material in the lab note
- OK to record public certificate fingerprint (`keytool -list -v` output, minus anything private) if useful for future reference

### Dependencies

- 020 (POC-B execution complete)

### Blocks

- E6 (clean baseline for upload work)

### Priority

- Medium
