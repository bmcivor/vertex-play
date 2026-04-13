## 4. (Contingent) Add shadowlands:5000 to Docker insecure-registries

**Epic:** E1 — Lab integration and Jenkins onboarding

**As a** pipeline developer
**I want** the Docker daemon configured to push to `shadowlands:5000` over plain HTTP
**So that** Jenkins can push images to the lab registry without TLS errors

### Contingency

This ticket is only actioned if P6 is red in ticket 001. If existing pushes to `shadowlands:5000` already work, this ticket is not needed and should be marked as cancelled with reason "P6 green — not required."

### Acceptance criteria

- `shadowlands:5000` is listed in `insecure-registries` in the Docker daemon configuration on the Jenkins host
- The change is made via vertex-studio Ansible, not by editing host files directly
- The Docker daemon is restarted and existing pipelines still push successfully
- The change is committed on a vertex-studio branch (may be the same branch as 002 or a separate one — decide at execution)

### Cross-repo

This ticket edits **vertex-studio**, not vertex-play. Requires explicit approval before touching the other repo.

### Dependencies

- 001 (P6 red triggers this ticket)

### Blocks

- 002 (cannot proceed with registry push verification until this is resolved)

### Priority

- High (if actioned)
