## 1. Verify lab prerequisites for Jenkins onboarding

**Epic:** E1 — Lab integration and Jenkins onboarding

**As a** pipeline developer
**I want** to verify that the lab infrastructure supports adding a new repo to Jenkins
**So that** cross-repo changes in E1 are not blocked by undiscovered environment issues

### Acceptance criteria

- P1 confirmed: vertex-studio inventory is editable and the Jenkins redeploy workflow is known to work (dry-run or recent successful run)
- P2 confirmed: Jenkins at `shadowlands:8083` is reachable and healthy (`curl -fsS http://shadowlands:8083/login` returns OK)
- P3 confirmed: the Jenkins container can run `docker` commands via the mounted host socket (observed in existing vertex-block or vertex-studio builds)
- P4 confirmed: the Jenkins container can push to `shadowlands:5000` without interactive auth (observed in existing pipeline logs or a throwaway push)
- P5 confirmed: `shadowlands:5000` is reachable from the Jenkins container (`curl http://shadowlands:5000/v2/` returns 200)
- P6 confirmed: the Docker daemon has `shadowlands:5000` in `insecure-registries`, or existing pushes already work over plain HTTP (inspect `/etc/docker/daemon.json` on the host, or verify via existing push logs)
- Each prerequisite is recorded as green or red
- If any prerequisite is red, an explicit stop-or-fix decision is made before proceeding

### Scope boundary

This ticket covers P1–P6 from `docs/implementation/builder-image.md` only. P7 (disk headroom), P8 (RAM), and P9 (outbound HTTPS to SDK/Maven endpoints) are deferred to E2 — they are lab capacity concerns, not Jenkins onboarding prerequisites.

### Dependencies

- None

### Blocks

- 002, 003 (all E1 work gates on this)
- If P6 is red, 004 must be completed before 002

### Priority

- High
