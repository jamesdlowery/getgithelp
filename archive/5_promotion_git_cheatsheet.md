# Git — Promote Dev to Main Cheat Sheet
> Linux · dev → release PR → main → tag → deploy

**Flow:** dev (fully tested) → release PR (web UI review) → main (production) → tag release → deploy

> **Legend:**
> - `[local]` — commands run in your terminal
> - `[web UI]` — actions taken in GitHub or GitLab
> - `[DANGER]` — production impact; proceed with care

---

## 1. Pre-Flight Checks `[local]`

| Command | Description |
|---|---|
| `git checkout dev && git pull origin dev` | Ensure local dev is fully up to date |
| `git log origin/main..dev --oneline` | List all commits on dev not yet in main — review before proceeding |
| `git diff origin/main..dev --stat` | Summary of files changed between dev and main |
| `git status` | Confirm dev is clean with no uncommitted changes |

> **Warning:** Never promote a dev branch that has failing tests, uncommitted changes, or unreviewed commits.

---

## 2. Open a Release PR: dev → main `[web UI]`

| Platform | Navigation |
|---|---|
| **GitHub** | Pull Requests → New pull request → base: `main` / compare: `dev` |
| **GitLab** | Merge Requests → New merge request → source: `dev` / target: `main` |

> **Tip:** Title the PR clearly, e.g. `Release v1.2.0`. Include a changelog or summary of what's shipping.
> Assign senior reviewers — this is going to production.

---

## 3. Review, Approve & Merge the PR `[web UI]`

| Action | Description |
|---|---|
| Reviewers approve the PR | At least one (preferably two) senior approvals required |
| All CI checks pass | Tests, linting, security scans — nothing merges red |
| Click "Merge pull request" → "Create a merge commit" | Use a merge commit to preserve the full dev history on main |

> **Warning:** Do not use squash or rebase merge strategies for a dev → main promotion.
> A merge commit clearly marks the release point in the history.

---

## 4. Tag the Release `[local]`

| Command | Description |
|---|---|
| `git checkout main && git pull origin main` | Switch to main and sync the merged state |
| `git tag -a v1.2.0 -m "Release v1.2.0"` | Create an annotated tag at the current HEAD of main |
| `git push origin v1.2.0` | Push the tag to the remote |
| `git tag -l` | List all tags to verify |

> **Tip:** Use semantic versioning: `vMAJOR.MINOR.PATCH`. Tags trigger CI/CD pipelines and create
> GitHub/GitLab release artifacts automatically.

**Semantic versioning guide:**
| Increment | When to use |
|---|---|
| `MAJOR` (v**2**.0.0) | Breaking changes — not backwards compatible |
| `MINOR` (v1.**2**.0) | New features — backwards compatible |
| `PATCH` (v1.2.**1**) | Bug fixes — backwards compatible |

---

## 5. Sync Dev With Main Post-Merge `[local]`

| Command | Description |
|---|---|
| `git checkout dev` | Switch back to dev |
| `git merge main` | Bring the merge commit back into dev to keep histories aligned |
| `git push origin dev` | Push the updated dev to remote |

> **Note:** Skipping this step causes dev to diverge from main over time, leading to messy future merges.

---

## 6. Emergency Rollback `[DANGER]`

> **Warning:** Always prefer `git revert` — it is safe and non-destructive. Only use
> `reset --hard` + force-push as an absolute last resort with full team awareness.

| Command | Description |
|---|---|
| `git revert HEAD && git push origin main` | **Safe** — creates a new revert commit, preserves full history |
| `git checkout main && git reset --hard <prev-tag>` | **Destructive** — hard reset to a previous tag |
| `git push --force-with-lease origin main` | Force-push after hard reset — only if branch protections allow it |

---

## 7. Full Sequence (Quick Reference)

```bash
# --- local: pre-flight ---
git checkout dev && git pull origin dev
git log origin/main..dev --oneline        # review commits shipping to prod

# --- web UI: open PR dev → main, get approvals, all CI green, merge ---

# --- local: tag the release ---
git checkout main && git pull origin main
git tag -a v1.2.0 -m "Release v1.2.0"
git push origin v1.2.0

# --- local: sync dev with main ---
git checkout dev
git merge main && git push origin dev
```
