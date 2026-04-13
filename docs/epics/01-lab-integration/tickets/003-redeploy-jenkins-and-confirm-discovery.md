## 3. Redeploy Jenkins and confirm vertex-play discovery

**Epic:** E1 — Lab integration and Jenkins onboarding

**As a** pipeline developer
**I want** Jenkins redeployed with the updated inventory and vertex-play confirmed as a multibranch project
**So that** E2 can rely on Jenkins-triggered builds for vertex-play

### Acceptance criteria

- Jenkins is redeployed using the existing vertex-studio playbook with the 002 branch applied
- vertex-play appears in the Jenkins UI at `shadowlands:8083` as a multibranch project
- Jenkins has indexed at least one branch from vertex-play
- Jenkins starts and all existing projects (vertex-block, vertex-studio) remain healthy after redeploy

### Cross-repo

This ticket runs a vertex-studio playbook. Requires explicit approval before executing.

### Rollback

If Jenkins fails to start after redeploy, revert the vertex-studio branch change from 002 and redeploy again. Do not patch Jenkins live.

### Dependencies

- 002

### Blocks

- E2 (builder image) — all E2 tickets depend on vertex-play being visible in Jenkins
- All subsequent epics that run via Jenkins

### Priority

- High
