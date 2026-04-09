# Git — Clone Existing Repo Cheat Sheet
> Linux · clone · explore · configure · work

**Workflow:** Remote repo → `git clone` → local working copy → branch → develop

---

## 1. Clone a Repository

| Command | Description |
|---|---|
| `git clone <url>` | Clone a repo into a new folder (folder named after repo) |
| `git clone <url> <directory>` | Clone into a specific folder name |
| `git clone --depth 1 <url>` | Shallow clone — latest snapshot only, no full history |
| `git clone -b <branch> <url>` | Clone and immediately check out a specific branch |

> **Tip:** Get the URL from the GitHub/GitLab repo page — use HTTPS for simplicity, SSH for key-based auth.

---

## 2. Explore After Cloning

| Command | Description |
|---|---|
| `cd <project>` | Navigate into the cloned folder |
| `git log --oneline -10` | View the last 10 commits |
| `git branch -a` | List all local and remote branches |
| `git remote -v` | Confirm the remote URL (should be `origin`) |
| `git status` | Confirm you are on the default branch with a clean tree |

---

## 3. Check Out a Branch

| Command | Description |
|---|---|
| `git checkout <branch>` | Switch to an existing local branch |
| `git checkout -b <branch>` | Create and switch to a new branch |
| `git checkout -b <branch> origin/<branch>` | Create a local branch tracking an existing remote branch |
| `git switch <branch>` | Modern alternative to `git checkout <branch>` |
| `git switch -c <branch>` | Modern alternative to `git checkout -b <branch>` |

> **Tip:** Always create a new branch before making changes — never work directly on `main` or `dev`.

---

## 4. Configure the Clone (If Needed)

| Command | Description |
|---|---|
| `git config user.name "Your Name"` | Set name for this repo only (omit `--global`) |
| `git config user.email "you@email.com"` | Set email for this repo only |
| `git remote set-url origin <new-url>` | Change the remote URL (e.g. HTTPS → SSH) |
| `git remote add upstream <url>` | Add a second remote (e.g. original repo when working from a fork) |

> **Tip:** Use `git config --global` to apply settings to all repos, or omit `--global` to apply to this repo only.

---

## 5. Stay Up to Date

| Command | Description |
|---|---|
| `git fetch origin` | Download remote changes without touching your working tree |
| `git pull` | Fetch and merge the tracked remote branch into current branch |
| `git pull --rebase` | Fetch and rebase local commits on top of remote (cleaner history) |
| `git pull origin <branch>` | Pull a specific remote branch into the current branch |

> **Tip:** Run `git fetch origin` regularly to stay aware of what's changed on the remote without committing to a merge.

---

## 6. Full Sequence (Quick Reference)

```bash
git clone <url>
cd <project>
git branch -a                          # see all available branches
git checkout -b <your-branch>          # create a working branch
# --- make your changes, then sync ---
git fetch origin                       # stay up to date
git pull --rebase                      # incorporate remote changes
```
