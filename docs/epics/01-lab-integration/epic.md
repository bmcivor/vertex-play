# E1 — Lab integration and Jenkins onboarding

**Status:** Tickets written.

## Purpose

Make vertex-play visible to Jenkins on the lab box (`shadowlands:8083`) as a multibranch project. Until this lands, no Jenkins-triggered build for vertex-play can run, so this is a precondition for E2 (builder image) and everything downstream.

This epic is **mostly cross-repo work in vertex-studio** — adding vertex-play to the `jenkins_repos` Ansible inventory and redeploying Jenkins. Vertex-play itself doesn't need any code changes for this epic; it just needs to be discoverable by Jenkins.

## In scope

- Add vertex-play to vertex-studio's `jenkins_repos` Ansible inventory.
- Redeploy Jenkins via the existing vertex-studio playbook.
- Confirm vertex-play appears in Jenkins as a multibranch project.
- Verify the Jenkins container can push to `shadowlands:5000` using the host Docker socket (covers P4 from [`builder-image.md`](../../implementation/builder-image.md)).
- Contingent: add `shadowlands:5000` to the Docker daemon `insecure-registries` if P6 is red.

## Out of scope

- Writing the bootstrap `Jenkinsfile` (E2).
- Writing `docker/Dockerfile` (E2).
- Lab capacity verification — disk and RAM headroom for Android builds (E2).
- Jenkins credentials for signing or Play API (E5 / E6).

## Cross-repo touch points

This epic edits **vertex-studio**, not vertex-play. Cross-repo changes require explicit approval per project rules. The vertex-studio changes are:

- Add a vertex-play entry to `jenkins_repos` in the Ansible inventory.
- Run the existing vertex-studio Jenkins redeploy playbook.
- Contingent insecure-registries change (only if P6 in [`builder-image.md`](../../implementation/builder-image.md) turns out red).

## Dependencies

None.

## Blocks

- E2 (the builder image cannot be built or pushed via Jenkins until this epic is done).
- All subsequent epics that run via Jenkins.

## Open questions

None significant. The vertex-studio inventory format is established, and the Jenkins redeploy procedure already works for vertex-block and vertex-studio itself. **No POC required for this epic.**

## Tickets

- [001 — Verify lab prerequisites for Jenkins onboarding](tickets/001-verify-lab-prerequisites.md)
- [002 — Add vertex-play to vertex-studio jenkins_repos inventory](tickets/002-add-vertex-play-to-jenkins-repos.md)
- [003 — Redeploy Jenkins and confirm vertex-play discovery](tickets/003-redeploy-jenkins-and-confirm-discovery.md)
- [004 — (Contingent) Add shadowlands:5000 to Docker insecure-registries](tickets/004-contingent-insecure-registries.md)
