## 18. Store keystore and passwords in Jenkins credentials

**Epic:** E5 — Signing

**As a** pipeline developer
**I want** the upload keystore and its passwords stored as Jenkins credentials
**So that** the signing pipeline can inject them without touching git

### Context

Same pattern as E4 016 (Play API JSON): secrets live in Jenkins, never in git. Three credentials here because the keystore file and the two passwords are separate secrets. The key alias is not a secret and can be hard-coded in the Gradle signing config in ticket 019.

Record all credential IDs — they're the strings the Jenkinsfile (in 020 and eventually E7) will reference.

### Acceptance criteria

- Keystore `.jks` added as a **Secret file** credential
- Keystore password added as a **Secret text** credential
- Key password added as a **Secret text** credential
- Credential IDs picked with stable, descriptive names (e.g. `vertex-play-upload-keystore`, `vertex-play-keystore-password`, `vertex-play-key-password`)
- Credential scope matches existing convention (global or vertex-play folder, same as E4 016)
- All three credential IDs documented in a working note for 020 and E7 to reference

### Verification

- Create a throwaway Jenkins pipeline job that injects all three:
  ```
  withCredentials([
    file(credentialsId: '<keystore-id>', variable: 'KEYSTORE_FILE'),
    string(credentialsId: '<keystore-pw-id>', variable: 'KEYSTORE_PASSWORD'),
    string(credentialsId: '<key-pw-id>', variable: 'KEY_PASSWORD')
  ]) {
    sh 'test -f "$KEYSTORE_FILE" && test -n "$KEYSTORE_PASSWORD" && test -n "$KEY_PASSWORD" && echo OK'
  }
  ```
- Do **not** echo or cat the passwords in the verification script
- Delete the throwaway job after verification

### Cleanup

- Local working copy of the `.jks` on the dev machine deleted once credentials confirmed in Jenkins
- Backup copy (from 017) retained — **not** deleted

### Dependencies

- 017 (keystore exists)
- E1 003 (Jenkins accessible)

### Blocks

- 020 (signing validation needs the credentials wired)

### Priority

- High
