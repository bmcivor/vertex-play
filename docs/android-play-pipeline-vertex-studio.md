Part of the hub [android-play-pipeline-plan.md](android-play-pipeline-plan.md). Task IDs and **Done** status live there.

---

## Integration with vertex-studio specifically

- **Jenkins**: Multibranch + `Jenkinsfile` in the **app repository** (same pattern as [docs/playbooks/jenkins.md](../vertex-studio/docs/playbooks/jenkins.md)); Android jobs will differ from the vertex-studio image pipeline—**this repo (`vertex-play`)** is the intended home once `Jenkinsfile` is added and the repo is listed in `jenkins_repos`.
- **Registry**: Use it to host `**jenkins-android-agent:tag`** (or build cache image) so rebuilds are fast and documented; not a substitute for Play distribution.
- **Secrets**: Today, sensitive values live in **Ansible Vault** for deploy-time config; **Play service account JSON** and **keystore** are **Jenkins credentials** (or a small, audited secret mechanism you add). The plan should spell out **rotation**, **backup of upload keystore**, and **who can decrypt**.
- **Network**: Webhooks already use Tailscale Funnel in docs; Play API and SDK downloads require **outbound HTTPS** from the build environment—confirm from the lab host.

### Does this become a project like `vertex-play`?

**Not automatically**—you split concerns into what the **lab platform** owns vs what the **Android / Play product** owns. [vertex-studio](../vertex-studio) stays the Ansible home for Jenkins, registry, and docs site; it does not need to contain your app source.

A repo named `**vertex-play`** is the **working name** here: it signals **Google Play** delivery as well as the Android app. In **P-01**, define what it contains:

1. **Lab-tooling / CI kit only** — Dockerfile(s) for the Android SDK build image, optional shared `Jenkinsfile` fragments or README for wiring `jenkins_repos`, and design notes. **No** store listing tied to this repo unless you also keep a throwaway sample app here for POCs.
2. **The actual shipped app** — Full Gradle project, `Jenkinsfile`, signing/play config as code; `**vertex-play`** is then the **product** repo (like **vertex-block** in the roadmap). Play Console and store listing refer to this application.
3. **Hybrid** — Same repo holds both a `samples/hello` module for lab smoke tests **and** the real app; higher coupling; only worth it if you want one clone for everything.

**Typical clean split:** **vertex-studio** (infra) + `**vertex-play`** (or your chosen app repo name) + optionally a small `**vertex-play-ci`** repo if you want CI Dockerfiles separate from app code so image rebuilds do not churn the app’s git history.

**P-01–P-03** capture the final naming and documentation outcome; rename the string if you change your mind later.

**When to decide P:** You do **not** need to finalize P before investigation. **I-01** (where the app repo lives) is enough to start **I**, **V**, and **POC-A**. Treat **P-01–P-02** as blocking only when you are about to **create a new repo**, **split CI Dockerfiles out of the app repo**, or **commit to a name** for docs and `jenkins_repos`. **P-03** waits until that naming is stable.
