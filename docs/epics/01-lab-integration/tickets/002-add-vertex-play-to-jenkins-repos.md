## 2. Add vertex-play to vertex-studio jenkins_repos inventory

**Epic:** E1 — Lab integration and Jenkins onboarding

**As a** pipeline developer
**I want** vertex-play added to the vertex-studio Ansible inventory that drives Jenkins job creation
**So that** Jenkins discovers vertex-play as a multibranch project after the next redeploy

### Acceptance criteria

- A new entry for vertex-play exists in the `jenkins_repos` variable in vertex-studio's Ansible inventory, matching the format of existing entries (vertex-block, vertex-studio)
- The change is committed on a vertex-studio branch, not on main
- No other inventory changes are included in the same commit

### Cross-repo

This ticket edits **vertex-studio**, not vertex-play. Requires explicit approval before touching the other repo.

### Dependencies

- 001 (all prerequisites green)
- 004 if P6 was red in 001

### Blocks

- 003

### Priority

- High
