# Repository Boundaries

What lives where so vertex-play and the lab stay unambiguous.

## vertex-play (this repo)

- Android builder Docker image (`docker/`)
- Reference `Jenkinsfile` for Android app pipelines
- Play upload tooling configuration (no secrets)
- Documentation: spec, reference, POC lab notes

## vertex-studio (sibling repo)

- Lab infrastructure: Ansible playbooks/roles, Jenkins deployment, Docker registry, Tailscale, observability stack
- Does not contain pipeline definitions or Android tooling
- vertex-play is added to Jenkins via `jenkins_repos` in vertex-studio inventory

## App repos (separate)

- Android application source code, Gradle project, app-specific `Jenkinsfile` (copied/adapted from vertex-play's reference)
- One repo per app, or a monorepo — vertex-play doesn't dictate app repo structure

## Secrets

- Lab secrets (Jenkins admin, GitHub PAT): vertex-studio Ansible Vault
- Pipeline secrets (upload keystore, Play API JSON): Jenkins credentials store, managed by operator
- No secrets in any git repo

## Links

- [scope.md](scope.md)
- [spec.md](spec.md) — pipeline specification
