## 19. Add Gradle signing config to test app

**Epic:** E5 — Signing

**As a** pipeline developer
**I want** the test app's `build.gradle.kts` configured to sign release builds from env vars
**So that** Jenkins can produce a signed release AAB without the keystore touching disk as a file in git

### Context

The signing config reads keystore path, passwords, and alias from environment variables. When the env vars are absent (local dev), release builds either fall back to unsigned (and `bundleRelease` may warn) or skip the signing config block — local developers don't need the real keystore. Debug builds continue to use the default debug keystore without any config.

The pattern: Jenkins injects env vars via `withCredentials`, Gradle reads them, the signing config applies. No signing config file is committed with actual secret values.

### Acceptance criteria

- `test-app/app/build.gradle.kts` has a `signingConfigs { create("release") { ... } }` block that reads from env vars:
  - `storeFile = file(System.getenv("UPLOAD_KEYSTORE_FILE") ?: "")`
  - `storePassword = System.getenv("UPLOAD_KEYSTORE_PASSWORD") ?: ""`
  - `keyAlias = "upload"` (hard-coded — matches 017)
  - `keyPassword = System.getenv("UPLOAD_KEY_PASSWORD") ?: ""`
- `buildTypes { release { signingConfig = signingConfigs.getByName("release") } }` references the config
- Release build gracefully degrades when env vars are missing (either skip signing config application or produce an unsigned AAB with a clear warning — pick one, document in ticket notes)
- Debug build still works without any env vars set (`./gradlew :app:assembleDebug` succeeds locally)
- No keystore passwords, paths, or alias values hard-coded beyond the alias string
- `.gitignore` updated if needed so no local keystore files can be committed to `test-app/`
- Committed on a working branch

### Notes

- The env var names chosen here (`UPLOAD_KEYSTORE_FILE`, `UPLOAD_KEYSTORE_PASSWORD`, `UPLOAD_KEY_PASSWORD`) need to match what 020's signing validation injects via `withCredentials` — pick names consistently across both tickets
- Kotlin DSL is assumed (`.kts`) — if the project ended up on Groovy DSL, adapt syntax

### Dependencies

- 017 (for the alias — "upload" unless changed)
- E3 012 (test app builds locally — this ticket modifies its build config)

### Blocks

- 020 (signing validation needs the signing config in place)

### Priority

- High
