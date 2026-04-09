# POC-A — Android builder image

**Status:** Draft plan. Not yet executed.

## Purpose

POC-A proves the lowest layer of the vertex-play pipeline: a reproducible Android builder image exists in `shadowlands:5000`, was produced by Jenkins (not a developer laptop), and can build a real Android app into an unsigned `.aab` inside a container.

POC-A does **not** cover signing (POC-B), Play upload (POC-C), the app-pipeline `Jenkinsfile` from [spec.md](../spec.md) section 2, or emulator / instrumented tests (POC-D).

**Scope boundary:** the bootstrap `Jenkinsfile` written in this POC is for **building vertex-play itself** (the builder image). It is not the reference Jenkinsfile that app repos will copy. Those are two different artefacts and two different POCs.

## What POC-A proves and does not prove

**Proves:**
- `docker/Dockerfile` builds via the Jenkins container (using the host Docker socket) with pinned versions, no `:latest` tags, no host-side Android SDK.
- The resulting image pushes to `shadowlands:5000` via Jenkins.
- A container started from that image can clone `github.com/android/architecture-samples` at a pinned commit and run `./gradlew :app:bundleRelease` to completion.
- The produced artefact is a valid `.aab` (unsigned) that passes a basic structural sanity check.

**Does not prove:**
- Signing works.
- Play Developer API upload works.
- The image is suitable for any Android app other than architecture-samples at the pinned commit.
- Instrumented / UI tests work.
- The builder image is the right long-term choice — that only emerges across multiple POCs.

## Inputs

| Input | Location | Status |
|-------|----------|--------|
| `docker/Dockerfile` | vertex-play root | Does not exist — T2 creates it |
| Bootstrap `Jenkinsfile` | vertex-play root | Does not exist — T3 creates it |
| `jenkins_repos` entry for vertex-play | vertex-studio Ansible inventory | Does not exist — T4 creates it (cross-repo) |
| Validation target app | `github.com/android/architecture-samples` at a pinned commit SHA | Public repo, cloned at execution time, not vendored |
| Base OS/JDK image | `eclipse-temurin` (or equivalent) | Exact pin resolved in G6 |

## Outputs

| Output | Destination | How it is verified |
|--------|-------------|--------------------|
| Builder image | `shadowlands:5000/android-builder:<pinned-tag>` | `curl http://shadowlands:5000/v2/android-builder/tags/list` shows the tag; image digest recorded in the lab note |
| Unsigned `.aab` | Inside the validation container under `architecture-samples/.../build/outputs/bundle/*Release/` (exact path confirmed at execution) | `file` reports a Zip archive; `unzip -l` shows `BundleConfig.pb` and `base/manifest/AndroidManifest.xml`; SHA256 recorded in the lab note |
| Lab note | `docs/pocs/poc-a.md` (new file) | Written by T8 |

## Prerequisites (P1–P9)

All must be true before T1 starts. None are assumed — T0 walks the checklist.

| # | Prerequisite | How to confirm | Recovery if missing |
|---|--------------|----------------|---------------------|
| P1 | vertex-studio inventory is editable by B and the Jenkins-redeploy workflow is known to work | Dry-run a non-destructive vertex-studio playbook | Stop — resolve in vertex-studio first, not inside POC-A |
| P2 | Jenkins on `shadowlands:8083` is reachable and healthy | `curl -fsS http://shadowlands:8083/login` | Stop — vertex-studio health issue |
| P3 | The Jenkins container can run `docker` commands via the mounted host socket | Inspect existing vertex-block / vertex-studio Jenkinsfiles — they already do `docker build` | If absent, resolve in vertex-studio |
| P4 | The Jenkins container can push to `shadowlands:5000` without interactive auth | Observe existing pipelines pushing; or a throwaway `docker push` test | If auth is required, record the credential name for use in T3/T4 |
| P5 | `shadowlands:5000` is reachable from the Jenkins container | `curl http://shadowlands:5000/v2/` returns `{}` or `200 OK` | Networking issue — fix in vertex-studio |
| P6 | The Jenkins Docker daemon has `shadowlands:5000` in `insecure-registries` if it is plain HTTP | Inspect `/etc/docker/daemon.json` on the Jenkins host, or verify existing pushes work | Add via vertex-studio Ansible, never by editing the host directly |
| P7 | Shadowlands has at least ~20 GB free disk for SDK + Gradle cache + image layers | `df -h /` on shadowlands | Free space or stop |
| P8 | Shadowlands has at least ~4 GB free RAM during a Gradle build | `free -h` during an existing build | If tight, proceed but record it in the lab note |
| P9 | Outbound HTTPS works from the Jenkins container to `dl.google.com` and `repo1.maven.org` | `curl -fsS https://dl.google.com/android/repository/repository2-1.xml` from inside the Jenkins container | Proxy / DNS — fix in vertex-studio |

## Open decisions — resolved at execution (gates G1–G8)

Each gate is a lookup, not a guess. No values are pre-filled in this plan. T1 executes the lookups and records the values in the lab note.

### G1 — Architecture-samples pinned commit and AGP version
- **Lookup:** clone `github.com/android/architecture-samples`, pick an explicit commit SHA (not `main`), read `gradle/libs.versions.toml` for the `androidGradlePlugin` version.
- **Record:** commit SHA, AGP version, date checked.

### G2 — JDK version
- **Lookup:** from G1's AGP version, consult the AGP/JDK compatibility table (`developer.android.com/build/releases/gradle-plugin#compatibility` — already referenced in `docs/reference/building-blocks.md`).
- **Policy:** pick the latest stable JDK listed as compatible for that AGP row. Resolve to an explicit Temurin (or equivalent) version — never `:latest`.
- **Record:** AGP row used, chosen JDK version, chosen base image tag (feeds into G6).

### G3 — Android SDK platform
- **Lookup:** from the architecture-samples checkout, read `compileSdk` and `targetSdk` (in the app module's `build.gradle.kts` or in the version catalog — location varies by project structure, confirm at execution).
- **Policy:** install the platform for `compileSdk` at minimum; install others if the project references them at runtime.
- **Record:** `compileSdk`, `targetSdk`, platforms installed.

### G4 — Android build-tools
- **Lookup:** from the architecture-samples checkout, read `android.buildToolsVersion` if declared, otherwise use the latest stable build-tools compatible with G3.
- **Policy:** pin to an explicit version, never `latest`.
- **Record:** chosen build-tools version and why (declared in the project, or latest stable for G3).

### G5 — cmdline-tools version
- **Lookup:** latest stable from `developer.android.com/studio` (command-line-tools-only section) — the zip filename contains an explicit version number.
- **Policy:** pin the URL and the SHA256 of the downloaded zip. The URL itself carries the version, so any future download bump is deliberate.
- **Record:** cmdline-tools version, download URL, SHA256.

### G6 — Base image pin
- **Lookup:** pick the Temurin (or equivalent) JDK image tag matching G2. Cross-reference the image's registry page for an explicit tag; prefer pinning by `@sha256:` digest for true reproducibility.
- **Policy:** explicit tag at minimum, digest preferred.
- **Record:** full `FROM` reference used in the Dockerfile.

### G7 — Image tag scheme for the registry
- **Lookup:** inspect vertex-block / vertex-studio Jenkinsfiles — how do they tag images in `shadowlands:5000`? (Short git SHA? Branch name? Semver?)
- **Policy:** match the existing lab convention so the registry stays consistent across projects.
- **Record:** chosen tag scheme and a worked example.

### G8 — Jenkins `docker-workflow` plugin requirement
- **Lookup:** the bootstrap `Jenkinsfile` in T3 only runs `sh 'docker build'` and `sh 'docker push'`. It does not use `agent { docker { ... } }`. Inspect existing Jenkinsfiles in the lab to confirm this pattern works without the `docker-workflow` plugin.
- **Policy:** if the bootstrap does not need the plugin, do not add it — a plugin install is a vertex-studio change and should be deferred to whichever POC actually requires `agent { docker { ... } }` syntax (likely the app-pipeline Jenkinsfile in spec section 2, not POC-A).
- **Record:** plugin inventory state and whether POC-A triggers any plugin additions.

## Task sequence

Each task records its inputs, steps, "done when", failure modes, and where results go. Tasks run in order; T0 is a gate on everything after it.

### T0 — Walk the P1–P9 prerequisites checklist

- **Inputs:** P1–P9.
- **Steps:** verify each prerequisite, record state (green/red) in the lab note.
- **Done when:** all P1–P9 are green, or an explicit stop decision has been made and escalated.
- **Failure modes:** any red prerequisite → stop POC-A and escalate; do not work around red prerequisites inside POC-A.
- **Records:** lab note, `## Prerequisites` section.

### T1 — Resolve version pins (execute gates G1–G8)

- **Inputs:** G1–G8 lookups.
- **Steps:**
  1. Clone architecture-samples to a scratch directory. Check out an explicit commit SHA, not `main`. Record it (G1).
  2. Read its version catalog and build files. Fill in G1 (AGP), G3 (compile/target SDK), G4 (build-tools if declared).
  3. Consult the AGP/JDK compatibility table. Fill in G2.
  4. Look up latest stable cmdline-tools. Fill in G5.
  5. Pick the Temurin tag matching G2. Fill in G6.
  6. Inspect existing lab Jenkinsfiles for the tag scheme. Fill in G7.
  7. Inspect existing lab Jenkinsfiles for plugin usage. Fill in G8.
- **Done when:** every gate G1–G8 has a recorded value in the lab note.
- **Failure modes:**
  - Architecture-samples uses an AGP version with no stable JDK available → fall back to the latest AGP/JDK row that is stable and flag architecture-samples as possibly needing an older-AGP fork or replacement.
  - cmdline-tools URL returns 404 → Google has moved the endpoint; halt and update the G5 lookup.
- **Records:** lab note, `## Version pins` section.

### T2 — Write `docker/Dockerfile`

- **Inputs:** G1–G6 values from T1.
- **Steps:**
  1. Create `docker/` at vertex-play root.
  2. `FROM` the base image pinned in G6.
  3. Install minimal system packages: `curl`, `unzip`, `ca-certificates`, `git`, `zip`, `file`. Pin to the base image's package manager versions where reasonable.
  4. Create a non-root user for Gradle to run as.
  5. `ENV ANDROID_HOME=/opt/android-sdk` (or another path — document the choice).
  6. Download cmdline-tools at the G5 version, verify against the recorded SHA256, extract to `$ANDROID_HOME/cmdline-tools/latest/`.
  7. `yes | sdkmanager --licenses` — non-interactive license acceptance. Fail the build if sdkmanager prompts.
  8. `sdkmanager "platform-tools" "platforms;android-<G3>" "build-tools;<G4>"` — every version explicitly pinned.
  9. Set `PATH` to include `$ANDROID_HOME/cmdline-tools/latest/bin`, `$ANDROID_HOME/platform-tools`, `$ANDROID_HOME/build-tools/<G4>`.
  10. Do **not** install Gradle. The app repo provides its own wrapper via `gradlew`.
  11. Smoke test as the final build step: `java -version && sdkmanager --list | head -20 && echo OK`.
- **Done when:** `docker build -t android-builder-local docker/` succeeds on a developer machine and the smoke test passes. (This local build is only for the developer's confidence — the real build happens via Jenkins in T6.)
- **Failure modes:**
  - License acceptance hangs → missing `yes |` pipe, or sdkmanager prompted for a new category. Fix in the Dockerfile, don't paper over.
  - `sdkmanager` not found → `PATH` or extraction layout wrong.
  - Build OOM → base image too heavy or too many packages.
- **Records:** commit the Dockerfile on a working branch, do not merge yet. Record the branch name in the lab note.

### T3 — Write the bootstrap `Jenkinsfile`

- **Inputs:** G7 (tag scheme), G8 (plugin confirmation), registry address `shadowlands:5000`.
- **Scope reminder:** this `Jenkinsfile` builds vertex-play's own builder image. It is **not** the reference app-pipeline Jenkinsfile from [spec.md](../spec.md) section 2. Two different files, two different purposes. Keep them separate.
- **Steps:**
  1. Create `Jenkinsfile` at vertex-play root. Match the style (scripted vs declarative) used by vertex-block / vertex-studio — do not introduce a third style.
  2. Single stage: "Build and push builder image".
  3. Compute the image tag per G7 (e.g. `TAG=$(git rev-parse --short HEAD)` if that is the lab convention).
  4. `sh 'docker build -t shadowlands:5000/android-builder:${TAG} docker/'`.
  5. Optional additional tag for discoverability (e.g. `stable`, branch name) — explicit pin, **never** `:latest`.
  6. `sh 'docker push shadowlands:5000/android-builder:${TAG}'` (and the optional tag).
  7. Post-build: print the pushed image digest for the lab note (`docker inspect --format='{{index .RepoDigests 0}}' ...`).
- **Done when:** the `Jenkinsfile` is committed on the same branch as the T2 Dockerfile. Not yet triggered — T6 triggers it.
- **Failure modes:**
  - Required plugin missing → should have been caught in G8; revisit.
  - Push fails due to auth or insecure-registries → should have been caught in P4/P6; revisit.
- **Records:** lab note links the committed Jenkinsfile.

### T4 — [CROSS-REPO: vertex-studio] Add vertex-play to `jenkins_repos`

- **Separate approval required.** Edits in a different repo than the one being worked on need explicit approval before touching — per the rules B has set.
- **Inputs:** vertex-play repo URL and branch strategy.
- **Steps (proposed, not applied):**
  1. Locate the vertex-studio Ansible inventory file that holds `jenkins_repos` (exact path confirmed at execution — do not fabricate here).
  2. Add a vertex-play entry matching the format of existing entries (vertex-block, vertex-studio itself).
  3. Commit on a vertex-studio branch, never on main.
- **Done when:** B has approved the vertex-studio change and it is committed on a branch, ready to deploy.
- **Failure modes:** inventory format has drifted since the spec was written → adapt to the current format, don't force the old one.
- **Records:** lab note links the vertex-studio branch and commit.

### T5 — [CROSS-REPO: vertex-studio] Redeploy Jenkins

- **Separate approval required.** Same reason as T4.
- **Inputs:** the T4 vertex-studio branch.
- **Steps (proposed, not applied):**
  1. Run the existing vertex-studio deploy workflow that reconfigures Jenkins. B knows the exact command; POC-A does not fabricate it.
  2. Confirm that Jenkins has discovered vertex-play as a new multibranch project at `shadowlands:8083`.
- **Done when:** vertex-play appears in the Jenkins UI with branches indexed.
- **Failure modes:** Jenkins fails to start after redeploy → revert the vertex-studio branch change and escalate. Do not patch Jenkins live.
- **Records:** lab note records the Jenkins project URL and the deploy command used.

### T6 — Trigger the bootstrap Jenkins build and verify the image

- **Inputs:** Jenkins has indexed vertex-play (T5), the T2/T3 branch is pushed to the vertex-play remote.
- **Steps:**
  1. Trigger a build on the T2/T3 branch via the Jenkins UI (or wait for the webhook if configured).
  2. Watch the build log for `docker build` and `docker push` completion.
  3. `curl http://shadowlands:5000/v2/android-builder/tags/list` — confirm the G7 tag is present.
  4. Record the image digest from the push output.
- **Done when:** image exists in `shadowlands:5000` under the G7 tag and its digest is recorded.
- **Failure modes:**
  - Build succeeded locally in T2 but fails in Jenkins → difference in Docker daemon version, storage driver, or network. Capture the build log, diagnose, fix the root cause, do not retry blindly.
  - Push fails with "server gave HTTP response to HTTPS client" → insecure-registries misconfigured (P6). Fix via vertex-studio Ansible, never by editing `daemon.json` on the host.
- **Records:** lab note gets image digest, full tag, Jenkins build URL, wall-clock build duration.

### T7 — End-to-end validation: build architecture-samples inside the image

- **Inputs:** pushed image from T6, architecture-samples commit SHA from G1.
- **Steps:**
  1. On B's dev machine or on shadowlands (document which was used): `docker pull shadowlands:5000/android-builder:<G7-tag>`.
  2. `docker run --rm -it -v /abs/path/scratch:/work shadowlands:5000/android-builder:<G7-tag> bash`.
  3. Inside the container: `cd /work && git clone https://github.com/android/architecture-samples.git && cd architecture-samples && git checkout <G1-SHA>`.
  4. `./gradlew :app:bundleRelease --no-daemon --stacktrace`.
  5. Locate the produced `.aab`. Path depends on architecture-samples' current module structure and flavour setup — confirm at execution, do not fabricate.
  6. `sha256sum` the `.aab`, record the hash.
  7. Sanity check: `unzip -l <aab>` should list `BundleConfig.pb` and `base/manifest/AndroidManifest.xml`. Anything missing = not a valid bundle.
- **Done when:** an `.aab` file exists at the expected path, passes the sanity check, and its SHA256 is recorded in the lab note.
- **Failure modes:**
  - Gradle fails with "incompatible JDK" → G2 was wrong; revisit.
  - Gradle fails with "SDK component not installed" → G3/G4/G5 missed a required component; revisit T2.
  - License prompt during Gradle → sdkmanager license acceptance did not cover everything; revisit T2 step 7.
  - Out of disk inside the container → bind-mount a larger scratch volume.
- **Records:** lab note gets the exact Gradle command, `.aab` path, SHA256, container image digest, and wall-clock build time.

### T8 — Write the POC-A lab note

- **Inputs:** everything recorded in T0–T7.
- **Location:** `docs/pocs/poc-a.md` (new file).
- **Structure:**
  - Header: POC-A, execution date, executor.
  - Prerequisites: P1–P9 state at execution.
  - Version pins: resolved G1–G8 values.
  - Artefacts produced: Dockerfile commit, Jenkinsfile commit, image tag + digest, `.aab` SHA256.
  - Cross-repo changes: links to the vertex-studio branch from T4/T5.
  - Failures encountered and how they were resolved.
  - Open questions to carry into POC-B.
- **Done when:** `docs/pocs/poc-a.md` exists and is committed to vertex-play on the same working branch as T2/T3 (or merged, if POC-A is fully accepted).

## Acceptance criteria — POC-A overall

POC-A is **done** when all of the following are true:

1. `docker/Dockerfile` exists in vertex-play with no `:latest` tags anywhere; every version is pinned explicitly to a value recorded in the lab note.
2. A bootstrap `Jenkinsfile` at vertex-play root builds and pushes the image via Jenkins — not via a developer laptop.
3. The image is present in `shadowlands:5000` under the G7 tag scheme, and its digest is recorded in the lab note.
4. A container from that image, given architecture-samples at the G1-pinned commit, produces an unsigned `.aab` via `./gradlew :app:bundleRelease`, and the `.aab` passes the T7 sanity check.
5. `docs/pocs/poc-a.md` captures all versions, digests, SHA256s, and any failures encountered.
6. Any vertex-studio changes made to support POC-A are on their own branch, with B's explicit approval, not merged without review.

## Cross-repo touch points

All edits outside vertex-play are listed here so they are visible and separable from vertex-play work.

| Task | Repo | Change | Approval status |
|------|------|--------|-----------------|
| T4 | vertex-studio | Add vertex-play entry to `jenkins_repos` in the Ansible inventory | Requires separate approval before editing |
| T5 | vertex-studio | Run the Jenkins redeploy playbook | Requires separate approval before running |
| Contingent | vertex-studio | Add `shadowlands:5000` to the Docker daemon `insecure-registries` if P6 is red | Requires separate approval; only if that prerequisite fails |
| Contingent | vertex-studio | Install a Jenkins plugin (e.g. `docker-workflow`) if G8 unexpectedly says the bootstrap needs one | Requires separate approval; only if G8 decides yes |

No edits to any Android app repo are part of POC-A. architecture-samples is cloned as read-only validation input; nothing is committed upstream to it.

## Open risks

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| architecture-samples drifts between the T1 lookup and the T7 execution | Low if we pin a SHA in G1 | Would invalidate T7 | Pin the SHA in G1, reuse it verbatim in T7 |
| Shadowlands disk pressure during the first SDK download | Medium — Android SDK caches are large | Build fails or corrupts | P7 headroom check before T1 |
| Registry push fails due to `insecure-registries` or auth | Medium — depends on lab state | Blocks T6 entirely | P4 and P6 catch it in T0; fix via vertex-studio, not manually |
| `sdkmanager` license list changes and blocks the unattended install | Low but historically happens | T2 hangs indefinitely | `yes \|` pipe in T2 step 7; fail fast on unexpected prompts |
| architecture-samples' required JDK/AGP is not "latest stable" and our base image is too new | Medium | G2 conflict | Resolve JDK from the AGP row, not from "latest JDK" — the ordering in T1 enforces this |
| cmdline-tools download URL format changes silently | Low but happens | T2 step 6 fails | G5 records the exact URL and SHA256; any change forces a deliberate bump |
| The lab host's Docker daemon does not support a Dockerfile feature used in T2 | Low | T6 fails | Keep Dockerfile syntax conservative; no cutting-edge BuildKit features unless confirmed |
| POC-A produces an `.aab` that `bundletool` would reject for Play | Unlikely for unsigned validation | Would need rework in POC-B | `unzip -l` sanity check in T7; full `bundletool validate` is deferred to POC-B |
| Insecure registry credentials leak into Jenkins build logs | Low | Credential exposure | Never echo `docker login` output; use Jenkins credentials bindings, not inline env vars |

## Related docs

| Doc | Relevance |
|-----|-----------|
| [../spec.md](../spec.md) section 1 | Original builder image description — this plan refines it |
| [../reference/building-blocks.md](../reference/building-blocks.md) | JDK/SDK/Gradle/signing requirements — feeds G1–G5 |
| [../reference/design-decisions.md](../reference/design-decisions.md) section 1 | "Where the Android toolchain runs" — Dockerized build is the chosen option; POC-A tests it |
| [../repo-boundaries.md](../repo-boundaries.md) | Explains the vertex-play / vertex-studio split — why T4/T5 need separate approval |
| `../pocs/poc-a.md` | Lab note, produced by T8 (does not exist until then) |
