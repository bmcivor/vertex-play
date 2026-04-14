## 17. Generate upload keystore

**Epic:** E5 — Signing

**As a** pipeline owner
**I want** a fresh upload keystore for the test app
**So that** Jenkins has an upload key to sign release AABs with

### Context

The upload keystore is what Jenkins signs AABs with. Google re-signs with the app signing key (held by Play App Signing) at upload time. Loss of the upload key is recoverable — request a reset via Play Console — but avoid that by backing up the keystore securely before deleting the local copy in ticket 018.

The `keytool` command produces a `.jks` (or `.keystore`) file. The keystore has a password; the key inside has an alias and its own password (which can be the same as the keystore password but keeping them distinct is safer).

### Acceptance criteria

- Keystore generated with `keytool`:
  ```
  keytool -genkey -v -keystore upload.jks -alias upload -keyalg RSA -keysize 2048 -validity 10000
  ```
  (10000 days ≈ 27 years — Play recommends long-validity upload keys)
- Keystore algorithm `RSA`, key size `2048` (Play minimum)
- Keystore password, key alias, and key password recorded in a secure password manager (not a file, not a chat message)
- `keytool -list -v -keystore upload.jks` confirms the key is present and RSA 2048
- Backup copy of the `.jks` file stored securely (password manager attachment, encrypted drive — decide and record where)
- Working copy exists on the dev machine for ticket 018 to consume

### Notes

- Certificate DN (name, org, locality) fields are not security-critical for upload keys, but they can't be changed after generation — fill sensibly but not identifying if desired
- Do **not** use the debug keystore Android Studio creates in `~/.android/debug.keystore` — that's for debug builds only

### Dependencies

- None

### Blocks

- 018 (needs the keystore file)
- 019 (needs the alias to reference in build.gradle.kts)

### Priority

- High
