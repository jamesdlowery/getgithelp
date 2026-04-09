# Git — Merge to Dev via PR Cheat Sheet
> Linux · feature branch → PR → dev

**Flow:** feature branch → push → open PR → review & approve → merge PR (web UI) → pull dev locally

> **Legend:**
> - `[local]` — commands run in your terminal
> - `[web UI]` — actions taken in GitHub or GitLab

---

## 1. Clean Up & Push Your Branch `[local]`

| Command | Description |
|---|---|
| `git status` | Confirm no uncommitted changes remain |
| `git push -u origin <your-branch>` | Push branch to remote and set upstream tracking |

> **Note:** The `-u` flag is only needed the first time. Subsequent pushes just need `git push`.

---

## 2. Open a Pull Request `[web UI]`

| Platform | Navigation |
|---|---|
| **GitHub** | Repo → Pull Requests → New pull request → base: `dev` / compare: `<your-branch>` |
| **GitLab** | Repo → Merge Requests → New merge request → source: `<your-branch>` / target: `dev` |

> **Tip:** Write a clear PR title and description — what changed, why, and any testing notes.
> Assign reviewers and link related issues.

---

## 3. Address Review Feedback `[local]`

| Command | Description |
|---|---|
| `git add -A && git commit -m "address review feedback"` | Commit changes requested by reviewers |
| `git push` | Push updates — PR refreshes automatically |

> **Note:** Repeat commit + push as many times as needed. The PR always reflects the latest state of your branch.

---

## 4. Resolve Conflicts (If Flagged in the PR) `[local]`

| Command | Description |
|---|---|
| `git checkout dev && git pull --rebase origin dev` | Sync dev locally first |
| `git checkout <your-branch>` | Switch back to your branch |
| `git merge dev` | Bring dev changes into your branch to surface conflicts |
| `nano <conflicting-file>` | Resolve `<<<` / `===` / `>>>` markers manually |
| `git add -A && git commit && git push` | Stage, complete merge commit, push — PR will show no conflicts |

> **Warning:** Every conflict marker (`<<<<<<<`, `=======`, `>>>>>>>`) must be removed before committing.
> Leaving one in will break the build.

**Conflict marker anatomy:**
```
<<<<<<< HEAD (dev)
  code from dev
=======
  code from your branch
>>>>>>> your-branch
```
Delete the markers, keep whichever code (or combination) is correct, then `git add` the file.

---

## 5. Merge the PR `[web UI]`

| Action | Description |
|---|---|
| Click **"Merge pull request"** (GitHub) or **"Merge"** (GitLab) | Choose your merge strategy |
| Confirm the merge | PR is closed; branch is merged into dev on the remote |

**Merge strategies:**
| Strategy | What it does |
|---|---|
| Merge commit | Keeps full branch history with a merge commit |
| Squash and merge | Condenses all branch commits into one commit on dev |
| Rebase and merge | Replays commits linearly onto dev — no merge commit |

> **Tip:** Check your team's convention. Most teams standardise on one strategy.

---

## 6. Sync Local Dev After the Merge `[local]`

| Command | Description |
|---|---|
| `git checkout dev` | Switch to local dev branch |
| `git pull origin dev` | Pull the merged state from remote into local dev |

---

## 7. Clean Up `[local]`

| Command | Description |
|---|---|
| `git branch -d <your-branch>` | Delete local branch (platform may auto-delete the remote branch) |
| `git push origin --delete <your-branch>` | Delete remote branch if not auto-deleted by the platform |
| `git fetch --prune` | Remove stale remote-tracking references locally |

> **Note:** Use `-D` instead of `-d` to force-delete a branch not yet fully merged (use with care).

---

## 8. Full Sequence (Quick Reference)

```bash
# --- local: push your branch ---
git push -u origin <your-branch>

# --- web UI: open PR, assign reviewers ---
# --- web UI: iterate on feedback (push more commits as needed) ---

# --- local: if conflicts are flagged in the PR ---
git checkout dev && git pull --rebase origin dev
git checkout <your-branch>
git merge dev              # resolve conflicts, then:
git add -A && git commit && git push

# --- web UI: merge the PR once approved ---

# --- local: sync and clean up ---
git checkout dev && git pull origin dev
git branch -d <your-branch>
git fetch --prune
```
