# E4 — Google Play developer enrollment and API access

**Status:** Tickets written.

## Purpose

Set up everything external to the lab that the pipeline needs to upload builds to Google Play: a Play Console developer account, a GCP project with the Play Developer API enabled, a service account with a JSON key, the right permissions in Play Console, and an app listing for the test app.

This is almost entirely manual work in Google's web consoles. It runs in parallel with E1/E2/E3 because nothing here depends on the builder image or lab infrastructure — but identity verification has an external waiting period (days to weeks), which is why this epic starts early.

## In scope

- Play Console developer account registration and identity verification
- GCP project creation (or selection) and Play Developer API enablement
- Service account creation and JSON key generation
- Linking GCP project to Play Console, granting service account permissions
- App listing creation in Play Console using E3's `applicationId`
- Storing the service account JSON key in Jenkins credentials

## Out of scope

- Upload keystore generation or storage (E5)
- Play App Signing enrollment (E5 — requires the app listing from this epic)
- The actual upload mechanism and tool choice (E6)
- Any store listing content beyond what's minimally required to create an internal-track release

## Dependencies

- E3 011 (for `applicationId` — needed in ticket 015 when creating the Play Console app listing)
- E1 003 (for Jenkins access — needed in ticket 016 when storing the JSON credential)

## Blocks

- E5 (Play App Signing enrollment needs the Play Console app listing)
- E6 (upload tooling needs both the credentials in Jenkins and the app listing as upload target)

## Note on spec.md section 3c

Spec section 3c describes creating a "throwaway app" with a temporary `applicationId`. This was superseded by [ADR-001](../../decisions/ADR-001-test-app-fixture.md): the test app lives in vertex-play and owns its `applicationId`. The Play Console listing in ticket 015 uses that ID, not a throwaway.

## Tickets

- [013 — Register Google Play developer account](tickets/013-register-play-developer-account.md)
- [014 — Set up GCP project and service account for Play API](tickets/014-gcp-project-and-service-account.md)
- [015 — Create test app listing in Play Console](tickets/015-create-play-console-app-listing.md)
- [016 — Store Play API credentials in Jenkins](tickets/016-store-play-credentials-in-jenkins.md)
