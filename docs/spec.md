# Android to Google Play — Pipeline Specification

CI/CD pipeline kit for building, signing, and publishing Android applications to Google Play. Runs on the vertex-studio lab infrastructure. Individual Android apps are inputs — this repo is the tooling, not an app.

## What vertex-play contains

| Component | Purpose |
|-----------|---------|
| `docker/` | Dockerfile for the Android builder image (JDK + Android SDK + cmdline-tools + Gradle wrapper support) |
| `Jenkinsfile` | Pipeline definition: build → test → sign → upload |
| `docs/` | This spec and supporting reference docs |
| Play upload config | Gradle Play Publisher, fastlane, or API scripts (no secrets — those live in Jenkins credentials) |

An Android app repo (e.g. a hypothetical `vertex-app-foo`) points its Jenkins multibranch pipeline at this tooling or copies the Jenkinsfile pattern. vertex-play does not contain Android application source code.

## Existing infrastructure (vertex-studio)

| Component | Address | Relevance |
|-----------|---------|-----------|
| Jenkins | `shadowlands:8083` | Multibranch pipelines, GitHub webhook triggers, Docker socket for builds |
| Docker Registry | `shadowlands:5000` | Store the Android builder image so rebuilds are fast |
| Registry UI | `shadowlands:8084` | Browse stored images |
| Tailscale Funnel | `shadowlands:443` → `:8083` | Public HTTPS for GitHub webhooks |
| Ansible Vault | vertex-studio inventory | Jenkins admin password, GitHub PAT (existing secrets) |

Jenkins already builds vertex-block and vertex-studio via multibranch pipelines with tag detection. Adding vertex-play follows the same pattern: add to `jenkins_repos` in vertex-studio inventory, redeploy Jenkins.

## Pipeline

```
GitHub (app repo) → webhook → Jenkins → Docker builder image → Gradle → Lint/Test → Sign AAB → Upload to Play track → Manual review in Play Console
```

### Stages

| Stage | What happens | Inputs | Outputs |
|-------|-------------|--------|---------|
| Trigger | GitHub webhook fires on push/tag | Git event | Jenkins build start |
| Environment | Pull or build the Android builder image | `docker/Dockerfile` from vertex-play | Running container with JDK + SDK |
| Build | `./gradlew :app:bundleRelease` | App source + Gradle wrapper | Unsigned AAB |
| Quality | `./gradlew lint test` | App source | Lint report, test results (fail = stop) |
| Sign | Apply upload keystore to AAB | Unsigned AAB + keystore from Jenkins credentials | Signed AAB |
| Upload | Push signed AAB to Play internal track | Signed AAB + Play API service account JSON from Jenkins credentials | Track artifact visible in Play Console |
| Review | Store listing, data safety, content rating, approval | Track artifact | Published app (manual, not automated) |

### Branch model

| Trigger | Pipeline behaviour |
|---------|--------------------|
| Pull request | Build + test only (no sign, no upload) |
| Merge to main | Build + test + sign (artifact stored, not uploaded) |
| Git tag | Build + test + sign + upload to internal track |

## Implementation

### 1. Android builder image

Build a Docker image containing the Android build toolchain. Store it in the lab registry.

**Location:** `docker/Dockerfile`

**Contents:**
- Base: Ubuntu or Debian slim
- JDK: Version compatible with target AGP (see [blocks](android-play-pipeline-blocks.md) for the JDK/AGP matrix — resolve before building)
- Android SDK: `cmdline-tools`, `platform-tools`, `build-tools` for target API, platform for target API
- License acceptance: `yes | sdkmanager --licenses` (required for unattended installs)
- No Gradle — the app repo provides its own wrapper via `gradlew`

**Acceptance criteria:**
- `docker build -t android-builder:latest docker/` succeeds
- Inside the container: `java -version` returns expected JDK, `sdkmanager --list` works, `$ANDROID_HOME` is set
- Image pushed to `shadowlands:5000/android-builder:latest`

**Open decision — JDK version:** Depends on which AGP version the first app targets. The [AGP/JDK compatibility table](https://developer.android.com/build/releases/gradle-plugin#compatibility) is the source of truth. Pin explicitly in the Dockerfile, do not use `latest`.

### 2. Jenkinsfile

Pipeline definition that any Android app repo can use (directly or as reference).

**Location:** `Jenkinsfile` (root of vertex-play, and copied/adapted per app repo)

**Structure:**
```
pipeline {
    agent { docker { image 'shadowlands:5000/android-builder:latest' } }
    stages {
        stage('Build')   { ./gradlew :app:bundleRelease }
        stage('Test')    { ./gradlew test lint }
        stage('Sign')    { sign AAB using keystore from Jenkins credentials }
        stage('Upload')  { conditional on tag: upload to Play via chosen tool }
    }
}
```

**Acceptance criteria:**
- PR builds run Build + Test stages, skip Sign and Upload
- Tag builds run all stages
- Build failure at any stage stops the pipeline
- Credentials (keystore, Play API JSON) are injected from Jenkins, not checked into the repo

**Open decision — Docker agent vs shell:** Jenkins currently uses the host Docker socket. The Jenkinsfile can either run stages inside the builder container (`agent { docker { ... } }`) or shell out to `docker run`. Docker agent is cleaner if the Jenkins Docker Pipeline plugin is installed (it isn't currently — add `docker-workflow` to plugins.txt in vertex-studio).

### 3. Play account and API access

Manual steps that cannot be automated. Must be completed before upload stages work.

**3a. Developer enrollment:**
- Register at [Play Console](https://play.google.com/console/signup)
- Pay one-time registration fee
- Complete identity verification (for Australia: see [Google's document list by country](https://support.google.com/googleplay/android-developer/answer/15633622), select Australia)
- Personal vs Organisation account affects required documents

**3b. GCP project and service account:**
- Create or select a GCP project
- Link it to the Play Console (Play Console → API access)
- Enable the Google Play Android Developer API
- Create a service account with a JSON key
- Grant the service account permissions in Play Console: minimum "Release to internal testing track" on the target app
- Store the JSON key in Jenkins as a secret file credential

**3c. Throwaway app for testing:**
- Create a test app in Play Console (temporary `applicationId`, e.g. `au.com.bmcivor.pipelinetest`)
- Use this for POC-C upload testing before wiring a real app
- Can be deleted/unpublished after validation

**Acceptance criteria:**
- Can authenticate to the Play Developer API using the service account JSON
- Can upload an AAB to the test app's internal track via API

### 4. Signing

Configure release signing so Jenkins can produce a signed AAB without secrets in git.

**4a. Upload keystore:**
- Generate with `keytool` (or use an existing one)
- Store in Jenkins as a credential (secret file for the `.jks`, secret text for the passwords)
- Backup the keystore securely — loss means requesting upload key reset from Google

**4b. Gradle signing config:**
- App's `build.gradle.kts` reads keystore path and passwords from environment variables
- Jenkinsfile injects these from Jenkins credentials
- Local development uses debug signing (no keystore needed)

**4c. Play App Signing:**
- Enroll the app in Play App Signing on first upload (Google holds the app signing key, you hold the upload key)
- This is a one-time, irreversible action per app

**Acceptance criteria:**
- `./gradlew :app:bundleRelease` inside the builder container produces a signed AAB
- `jarsigner -verify` confirms the AAB is signed with the upload key
- Keystore and passwords are not present in any git repo

### 5. Upload tooling

Automate AAB upload to a Play track from Jenkins.

**Options (choose one):**

| Tool | Pros | Cons |
|------|------|------|
| Gradle Play Publisher (GPP) | Gradle-native, version in build file | Couples upload to Gradle; plugin version churn |
| fastlane supply | Well-documented, widely used | Ruby dependency in builder image |
| Raw Play Developer API | No extra dependencies | More code to write and maintain |

**Acceptance criteria:**
- From Jenkins, a tag build uploads a signed AAB to the internal testing track
- Upload fails clearly if credentials are missing or invalid
- `versionCode` is monotonically increasing (derived from tag, build number, or explicit bump)

### 6. Lab capacity (vertex-studio)

Verify the lab host can handle Android builds.

| Resource | Requirement | How to check |
|----------|-------------|--------------|
| Disk | Android SDK + Gradle caches: ~10-15 GB per app | `df -h` on shadowlands |
| RAM | Gradle + lint: 4 GB minimum for the build container | `free -h` during a build |
| Registry | Builder image stored at `shadowlands:5000` | Existing; verify push works |
| Network | Outbound HTTPS for SDK downloads, Play API, Gradle dependencies | `curl https://dl.google.com/android/repository/repository2-1.xml` from shadowlands |

**Acceptance criteria:**
- Builder image builds on shadowlands without running out of disk or memory
- Gradle build completes inside the container

## POC sequence

Execute in order. Each POC produces a short lab note under `docs/pocs/` documenting what worked, exact versions, and any failures.

| POC | What it proves | Steps | Done when |
|-----|---------------|-------|-----------|
| A — Docker toolchain | Builder image works, `bundleRelease` succeeds in container | Build image → run container → clone a sample app → `./gradlew :app:bundleRelease` | Unsigned AAB produced |
| B — Signing in CI | Jenkins can sign an AAB using credentials | Add keystore to Jenkins → Jenkinsfile injects env vars → `bundleRelease` with signing config | `jarsigner -verify` passes on output AAB |
| C — Play upload | AAB reaches Play internal track via API | Create throwaway app → configure upload tool → run upload stage | AAB visible in Play Console internal track |
| D — Instrumented tests (optional) | Emulator feasibility on lab hardware | Attempt headless emulator in Docker or evaluate Firebase Test Lab cost | Go/no-go decision documented |

## Secrets inventory

| Secret | Where it's stored | Who manages it |
|--------|--------------------|----------------|
| GitHub PAT | vertex-studio Ansible Vault → Jenkins env | Existing |
| Upload keystore (.jks) | Jenkins secret file credential | You — backup required |
| Keystore password | Jenkins secret text credential | You |
| Key password | Jenkins secret text credential | You |
| Play API service account JSON | Jenkins secret file credential | You — revoke/recreate via GCP |

## Related docs

| Doc | Contents |
|-----|----------|
| [scope.md](scope.md) | Repo scope: single repo for pipeline tooling + docs |
| [repo-boundaries.md](repo-boundaries.md) | What lives in vertex-play vs vertex-studio |
| [reference/building-blocks.md](reference/building-blocks.md) | JDK/SDK/Gradle/signing requirements table |
| [reference/design-decisions.md](reference/design-decisions.md) | Design decisions: Docker vs host SDK, test strategy, upload tool, branch model |
