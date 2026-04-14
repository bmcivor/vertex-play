## 16. Store Play API credentials in Jenkins

**Epic:** E4 — Google Play developer enrollment and API access

**As a** pipeline developer
**I want** the Play Developer API service account JSON stored as a Jenkins secret file credential
**So that** the Jenkinsfile can inject it into upload stages without the key ever appearing in a git repo

### Context

Small but distinct concern: the JSON key from 014 needs to live in Jenkins credentials before E6 can reference it. This keeps the secret out of git and matches the pattern already used for the GitHub PAT (vertex-studio Ansible Vault) and future signing credentials (E5).

Record the Jenkins credential ID here — it's the string E6 will reference in the Jenkinsfile.

### Acceptance criteria

- JSON key from 014 added to Jenkins as a **Secret file** credential (not Secret text, not Username with password)
- Credential ID recorded (e.g. `play-api-service-account-json`) — pick a stable ID because the Jenkinsfile will reference it by name
- Credential scope set appropriately (global, or scoped to vertex-play folder if folder-scoped credentials are in use in the lab — match existing convention)
- Local copy of the JSON key deleted from the dev machine once confirmed working in Jenkins
- Credential ID documented in a working note for E6 to reference

### Verification

- Create a throwaway Jenkins pipeline job (or use an existing test job) that does `withCredentials([file(credentialsId: '<id>', variable: 'KEY_FILE')]) { sh 'ls -la $KEY_FILE && head -1 $KEY_FILE | grep -q "{"' }` — confirms the file is injected and looks like JSON
- Delete the throwaway job after verification

### Dependencies

- 014 (JSON key exists)
- E1 003 (Jenkins redeployed and vertex-play visible)

### Blocks

- E6 (upload tooling references this credential)

### Priority

- Medium
