## 13. Register Google Play developer account

**Epic:** E4 — Google Play developer enrollment and API access

**As a** pipeline owner
**I want** an active Google Play developer account with identity verification complete
**So that** I can create apps in Play Console and grant API access for the pipeline

### Context

This is the long-tail gate for E4. Identity verification is a manual process on Google's side and can take days to weeks. Everything else in E4 waits on this ticket.

Account type (Personal vs Organisation) affects which documents are required. For Australia, Google lists the accepted documents [here](https://support.google.com/googleplay/android-developer/answer/15633622) — select Australia on that page to see the country-specific list.

### Acceptance criteria

- Account type chosen (Personal or Organisation) — record choice and reason in POC-B or POC-C lab note when it's eventually written
- Registration completed at [Play Console signup](https://play.google.com/console/signup)
- Registration fee paid
- Identity verification documents submitted
- Identity verification approved by Google — this is the gate
- Can log into Play Console and see the developer dashboard
- No apps need to exist yet (ticket 015 creates the first one)

### Dependencies

- None

### Blocks

- 014 (needs an active Play Console account to link to GCP)
- 015 (needs Play Console access to create the app listing)

### Priority

- High
