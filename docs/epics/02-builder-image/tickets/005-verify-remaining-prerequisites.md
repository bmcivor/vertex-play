## 5. Verify remaining lab prerequisites (P7–P9)

**Epic:** E2 — Builder image

**As a** pipeline developer
**I want** to confirm the lab has sufficient disk, RAM, and outbound network access for Android builds
**So that** the builder image build and Gradle execution do not fail due to resource constraints

### Acceptance criteria

- P7 confirmed: shadowlands has at least ~20 GB free disk for SDK + Gradle cache + image layers (`df -h /`)
- P8 confirmed: shadowlands has at least ~4 GB free RAM during a build (`free -h` during an existing build, or idle baseline if no build is running)
- P9 confirmed: outbound HTTPS works from the Jenkins container to `dl.google.com` and `repo1.maven.org` (`curl -fsS https://dl.google.com/android/repository/repository2-1.xml` from inside the Jenkins container)
- Each prerequisite is recorded as green or red
- If any prerequisite is red, an explicit stop-or-fix decision is made before proceeding

### Scope boundary

E1 ticket 001 covered P1–P6 (Jenkins, Docker socket, registry). This ticket covers the remaining P7–P9 (lab capacity and network) from [`builder-image.md`](../../../implementation/builder-image.md).

### Dependencies

- E1 003 (Jenkins redeployed and vertex-play visible)

### Blocks

- 006, and transitively everything else in E2

### Priority

- High
