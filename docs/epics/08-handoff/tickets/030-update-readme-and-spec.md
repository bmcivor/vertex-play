## 30. Update README.md and spec.md

**Epic:** E8 — Handoff and documentation

**As a** pipeline developer
**I want** README.md and spec.md to reflect the final state of the project
**So that** they are accurate references rather than pre-project planning documents

### Context

Both files were written before the project executed. The POCs will have confirmed or invalidated assumptions. This ticket is a targeted review and correction pass — not a rewrite.

### Acceptance criteria

**README.md:**
- Describes what vertex-play is and what it contains (builder image, reference Jenkinsfile, docs)
- Links to `docs/adoption.md` as the starting point for app developers
- Links to `spec.md` for pipeline design detail
- Reflects the final repo structure (directories that exist after all epics are complete)

**spec.md:**
- Section 3c (throwaway app for testing) updated to reflect ADR-001: the test app in `test-app/` served this role, not a throwaway `applicationId`
- Upload tool section updated to reflect ADR-005 decision (GPP chosen)
- Any version numbers, tool names, or assumptions that the POCs proved wrong are corrected
- Sections that are now fully implemented are noted as such (or left as-is if they read correctly as design docs — use judgement)
- Do not rewrite sections that are still accurate — targeted corrections only

### Notes

- Read each POC lab note before making corrections — the lab notes are the authoritative record of what actually happened
- If a spec assumption held up exactly, leave that section alone

### Dependencies

- 029 (adoption guide written — README links to it)
- All POC lab notes complete

### Priority

- Medium
