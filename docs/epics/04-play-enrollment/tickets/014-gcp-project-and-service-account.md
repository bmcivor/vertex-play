## 14. Set up GCP project and service account for Play API

**Epic:** E4 — Google Play developer enrollment and API access

**As a** pipeline owner
**I want** a GCP project with the Play Developer API enabled and a service account JSON key
**So that** Jenkins can authenticate to Google Play for uploads

### Context

Play Console uses Google Cloud for API access. The service account's JSON key is the credential Jenkins will eventually present when calling the Play Developer API. This ticket produces that key and grants the service account the permissions it needs in Play Console.

The JSON key is a high-value secret — it should not be committed anywhere, emailed, or pasted into chat. It moves from download to Jenkins credential store in ticket 016 and should be deleted from any intermediate location after.

### Acceptance criteria

#### GCP project

- GCP project created (or existing one selected) — record the project ID
- Google Play Android Developer API enabled on the project

#### Service account

- Service account created in the GCP project — record the service account email (`<name>@<project>.iam.gserviceaccount.com`)
- JSON key generated and downloaded to a known local location
- Key file permissions restricted (e.g. `chmod 600`) on the local machine until it's moved to Jenkins in 016

#### Play Console linkage

- GCP project linked in Play Console → Setup → API access
- Service account appears in Play Console → API access under "Service accounts"
- Service account granted permission: at minimum, "Release to production, exclude devices, and use Play App Signing" on the test app (scope this to the test app once 015 creates it, or account-level if more convenient for now — narrow it later)

#### Verification

- Can authenticate to the Play Developer API using the JSON key — e.g. `gcloud auth activate-service-account --key-file=<path>` and a simple API call to list apps succeeds, **or** note that verification is deferred to E6 when the upload tool is chosen
- JSON key path **not** committed to any git repo

### Dependencies

- 013 (Play Console account active — required to link GCP project)

### Blocks

- 016 (needs JSON key to store in Jenkins)

### Priority

- High
