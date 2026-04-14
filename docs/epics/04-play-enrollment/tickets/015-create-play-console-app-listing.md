## 15. Create test app listing in Play Console

**Epic:** E4 — Google Play developer enrollment and API access

**As a** pipeline owner
**I want** the test app registered in Play Console with its `applicationId`
**So that** E5 can enrol the app in Play App Signing and E6 has a valid upload target

### Context

Creating the Play Console listing reserves the `applicationId` on Google's side and creates the destination for uploads. No real store metadata is needed at this stage — only enough to create a release on the internal testing track.

The `applicationId` used here **must match** the value chosen in E3 011. Play Console treats this as the permanent identifier for the app. Play App Signing enrollment in E5 is one-time and irreversible per `applicationId`, so a mismatch here means starting over with a new app listing.

This ticket supersedes spec.md section 3c, which described a "throwaway app" with a temporary ID. Per ADR-001, the test app lives in vertex-play and owns its `applicationId`.

### Acceptance criteria

- App created in Play Console using the `applicationId` from E3 011
- App name is clearly marked as a test fixture (e.g. "vertex-play test app" — not something that could be confused with a real release)
- Default language and category selected (any reasonable value — this is a test fixture, not a real listing)
- Whatever minimum metadata Play Console requires to save the app as a draft is completed — **stop there**, do not proceed with full store listing content, screenshots, privacy policy, or data safety beyond what's strictly required to enable internal track uploads
- App visible in Play Console → All apps
- No release has been created yet — that happens when the upload tooling in E6 first uploads an AAB

### Notes

- Play Console's required fields change over time; treat the UI prompts as the source of truth, not any static checklist
- If a privacy policy URL is required even for internal tracks, link to a minimal placeholder (record in the lab note what was used)

### Dependencies

- 013 (Play Console account active)
- E3 011 (`applicationId` chosen)

### Blocks

- E5 (Play App Signing enrollment needs the listing to exist)
- E6 (upload target)

### Priority

- High
