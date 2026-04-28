## 31. mkdocs setup (optional)

**Epic:** E8 — Handoff and documentation

**As a** pipeline developer
**I want** vertex-play's docs served via mkdocs
**So that** the documentation is browsable on the lab in the same way vertex-studio's docs are

### Context

Optional ticket. vertex-studio has a mkdocs setup (see vertex-studio playbook). This ticket mirrors that for vertex-play. Can be deferred or dropped without affecting the handoff — `docs/adoption.md` is readable as plain markdown regardless.

Check the vertex-studio mkdocs playbook (`vertex-studio/docs/playbooks/mkdocs.md`) for the existing setup pattern before starting.

### Acceptance criteria

- `mkdocs.yml` at the repo root configured with the vertex-play doc structure
- Navigation covers: adoption guide, spec, reference docs, ADRs, POC lab notes
- Docs browsable locally via `mkdocs serve`
- Deployment approach documented (serve from lab host, or build to static files) — match vertex-studio's approach
- `docs/` directory structure unchanged — mkdocs configured to match what exists, not the other way around

### Notes

- This is the lowest-priority ticket in the project — defer if time is short
- If vertex-studio's mkdocs setup requires Ansible changes to add vertex-play, note the dependency on vertex-studio rather than making the change here

### Dependencies

- 029, 030 (docs are in final state before wiring up navigation)

### Priority

- Low (optional)
