# Cursor plan + docs — audit and remediation backlog

**Purpose:** Capture a review of `.cursor/plans/android_pipeline_vertex-studio_607b29d1.plan.md`, `docs/android-play-pipeline-plan.md`, and related split docs — gaps, duplicate truth, and a concrete backlog to bring the system “up to scratch.” **No changes below are applied until you decide.**

**Date:** 2026-04-01

---

## Part 1 — Audit (copy of findings)

### 1.1 Source-of-truth mess

| Issue | Detail |
|--------|--------|
| **Two full narratives** | The Cursor plan file embeds a long markdown body (~lines 230+) that duplicates content also living in `docs/android-play-pipeline-*.md` (hub + splits). Edits can fix one copy and not the other. |
| **Conflicting “canonical” story** | Todo **F-05** points at `docs/android-play-pipeline-plan.md`. The Cursor file also ends with “Canonical copy in vertex-play” — sensible — but the **YAML + duplicate body** still compete with the repo. |
| **Hub vs Cursor body drift** | `docs/android-play-pipeline-plan.md` is a **short hub** (tracking, recorded context, related-doc table). The **Cursor plan body** still contains a **long** “Recorded context” and full sections (submission, blocks, forks, etc.) that do not match the split layout. |

**Risk:** You think you fixed the plan in one place; the other still lies.

---

### 1.2 Missing todos (implicit work not in YAML)

| Gap | Why it bites |
|-----|----------------|
| **Minimal Android / Gradle in `vertex-play`** | No `id` for “add `:app` + Gradle wrapper + `./gradlew` succeeds.” **I-02** onward and **A-05/A-06** assume this exists. |
| **`jenkins_repos` wiring** | Described as operator-owned prose; **no todo** for “add `vertex-play` to vertex-studio inventory, redeploy Jenkins, confirm multibranch job sees the repo.” |
| **Play Console app vs “throwaway”** | **II-02** targets a **Play Console** throwaway for API experiments. **Registering the real app** tied to your real `applicationId` can be a separate console track — not its own row today. |
| **Ongoing policy drift** | No periodic “target API / Play policy / AGP” review — you will hit surprises on later submissions. |

---

### 1.3 Dependency / ordering problems (hidden DAG)

| Todo | Problem |
|------|---------|
| **A-01** | “Justify JDK vs AGP” — **AGP comes from the project.** Without an explicit scaffold step, **A-01** is either blocked or hand-wavy. |
| **A-03** | “Pin platforms; match **build contract from Phase I**” — should follow **I-09** (or at least **I-02–I-07**). Order implied, not encoded. |
| **III-*, IV-*** | Assume manifest, package, signing — **after** real app exists. |
| **B-*** | Logically after **A-06** (artifact to sign). Not stated in YAML. |
| **C-02** | Needs **II-04–II-08** plus credential handling. Easy to start too early. |
| **V-*** | Can parallel **II-00**, but egress (**V-03**) should match the **same** build context as **A-*/C-*** or you verify twice. |

---

### 1.4 Naming confusion

| Item | Issue |
|------|--------|
| **II-02** | “Throwaway app” = **Play Console** side. **Repo `app` module** = **vertex-play** side. Same word **app** in two worlds. |
| **I-01** | “Record `applicationId` when Gradle exists” overlaps the **missing scaffold** until split clearly. |

---

### 1.5 Count check

- **74** YAML `id:` rows — matches “~74” in the hub **until** you add new todos (then the narrative “74 rows” must be updated).

---

### 1.6 README / discovery

- **README** links hub + P-01 / P-02 / I-01 but does **not** say that **Cursor’s YAML** is the machine-readable todo list (if you still treat it that way).

---

## Part 2 — Remediation backlog (full list with examples)

Below: **what to do**, **example** of done-looking output, **notes**. Pick what you want; nothing here is mandatory.

### A. Canonical location (pick one strategy)

| # | Action | Example of “done” |
|---|--------|-------------------|
| A1 | Declare **single** checklist source: repo markdown only, **or** Cursor YAML only, **or** repo hub + YAML mirrors IDs. | One paragraph at top of hub: “Authoritative task IDs live in `.cursor/plans/...`” *or* “Authoritative list is this file; Cursor is export-only.” |
| A2 | **Remove or stub** the duplicate markdown body in `.cursor/plans/...` after line `---` (keep YAML + short link to `docs/`). | Cursor file = frontmatter todos + 15 lines: links to `android-play-pipeline-plan.md` and split docs. |
| A3 | If keeping **both**, add a **sync rule**: “Any todo/content change: edit X first, then copy to Y.” | Bullet in hub + one line in Cursor overview. |

---

### B. YAML todos — add missing rows

| # | Suggested id | Suggested content (example) | Notes |
|---|--------------|-------------------------------|--------|
| B1 | `i-00-android-scaffold` or `i-01b-minimal-app` | “Add minimal Android project: Gradle wrapper, `:app`, `applicationId`; `./gradlew :app:assembleDebug` (or `bundleRelease`) succeeds locally **or** document external sample repo path + branch used for CI.” | Insert **after `i-01`**, **before `i-02`**. Renumber narrative “I-01…I-09” → “I-00… I-09” only if you want strict ID alignment (optional). |
| B2 | `ops-01-jenkins-repo` | “Add `vertex-play` to `jenkins_repos` (or documented equivalent), redeploy Jenkins, confirm multibranch pipeline visible / webhook path documented.” | Operator-owned; still worth a checkbox so it is not lost between A-08 and real runs. |
| B3 | (Optional) `ii-02b-play-app-real` | “Create/register **production** Play app entry for target `applicationId` (distinct from II-02 throwaway if you use two).” | Skip if one Play app covers both API tests and shipping. |

---

### C. YAML todos — clarify dependencies (lightweight)

| # | Action | Example |
|---|--------|---------|
| C1 | Append **“Requires: …”** to ambiguous todos in `content` strings. | `a-03`: append “Requires: I-09 build contract (or interim I-02–I-07 pins).” |
| C2 | Add **`docs/plan-todo-dependencies.md`** — a table: `id` → `blocked by` → `notes`. | Markdown only; no tooling. |
| C3 | Rename **II-02** display text in docs (not necessarily YAML id): “Play Console **API test** app / package.” | Reduces confusion with `:app` module. |

---

### D. Docs structure (options — you said you’re open, not required)

| # | Option | Pros | Cons |
|---|--------|------|------|
| D1 | **Keep current hub + 6 splits**; fix Cursor file to **link only**. | Less rewrite; matches current repo. | Still many files to open. |
| D2 | **Collapse splits** back into one `android-play-pipeline-plan.md` for reading; keep **this audit** + YAML as execution truth. | One read path. | Long file again. |
| D3 | **New `docs/plan/`** folder: `README.md` (index), `00-hub.md`, `phase-I.md`, … | Clear navigation. | Moves paths; update README links. |
| D4 | **Track status only in hub** (Done table); detail files stay **timeless** (no status). | Clear separation. | Requires discipline. |

---

### E. vertex-play README

| # | Action | Example |
|---|--------|---------|
| E1 | Add bullet: where **todo IDs** live (Cursor path). | “Task IDs: `.cursor/plans/android_pipeline_vertex-studio_607b29d1.plan.md` (open in Cursor Plans).” |
| E2 | Link **this audit**: `docs/plan-audit-and-remediation.md`. | One line under Plan / checklist. |

---

### F. Hygiene / consistency passes

| # | Action | Example |
|---|--------|---------|
| F1 | Fix broken markdown in Cursor body if body kept: `` `**vertex-play`** `` → **vertex-play**. | Same sweep in split docs if any remain. |
| F2 | Update **“~74 rows”** everywhere if you add B1–B3 (e.g. 76). | Hub granular index + Taiga bullets. |
| F3 | **F-05** rewrite: point to **hub + dependency doc** instead of vague “keep in sync.” | “Quarterly: diff Cursor YAML vs hub Recorded context; update F-05 evidence.” |

---

### G. Research spikes (if you want external validation)

| # | Topic | Why |
|---|--------|-----|
| G1 | **AGP + JDK matrix** for your chosen AGP line (official table). | Locks **A-01** to evidence. |
| G2 | **Play Developer API** minimum IAM / OAuth scopes for internal track upload only. | Narrows **II-07** risk. |
| G3 | **Jenkins + Docker socket** pattern vs **Kaniko/buildkit** — whether you need doc updates for Android image builds. | Lab-specific fork. |

---

## Part 3 — Suggested decision order (for you)

1. Read Part 1 + 2.  
2. Choose **A** (canonical strategy).  
3. Choose **B** (which new todos to add).  
4. Choose **C** (dependency doc vs inline only).  
5. Choose **D** (restructure or not).  
6. Then apply changes in **one** place first (repo **or** Cursor), then mirror per A.

---

## Related files

- Hub: [android-play-pipeline-plan.md](android-play-pipeline-plan.md)
- Cursor plan (outside repo path): `.cursor/plans/android_pipeline_vertex-studio_607b29d1.plan.md`
