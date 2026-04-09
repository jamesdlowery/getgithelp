# Git — New Repo Cheat Sheet
> Linux · init · stage · commit · push

**Workflow:** Working Dir → Staging Area `git add` → Local Repo `git commit` → Remote `git push` → GitHub / GitLab

---

## 1. Create & Initialize

| Command | Description |
|---|---|
| `mkdir <project-name>` | Create a new project folder |
| `cd <project-name>` | Navigate into that folder |
| `git init` | Initialize an empty Git repo (creates `.git/`) |

> **Tip:** Already have a remote? Use `git clone <url>` instead — no need to `git init`.

---

## 2. Add Content

| Command | Description |
|---|---|
| `touch README.md` | Create an empty README file |
| `echo "# My Project" >> README.md` | Append content to a file from the terminal |
| `git status` | See which files are untracked / modified |
| `git add <file>` | Stage a specific file |
| `git add .` | Stage *all* new and modified files in current directory |
| `git add -p` | Interactively stage specific hunks (partial add) |

> **Tip:** Create a `.gitignore` before `git add .` to exclude files like `node_modules/` or `.env`.

---

## 3. Initial Commit

| Command | Description |
|---|---|
| `git commit -m "Initial commit"` | Commit staged files with a message |
| `git log --oneline` | Verify the commit was recorded |
| `git branch -M main` | Rename default branch to `main` (if needed) |

> **Tip:** First-time setup — configure your identity once:
> ```bash
> git config --global user.name "Your Name"
> git config --global user.email "you@email.com"
> ```

---

## 4. Connect Remote & Push

| Command | Description |
|---|---|
| `git remote add origin <url>` | Link local repo to a remote (GitHub, GitLab, etc.) |
| `git remote -v` | Verify the remote URL is set correctly |
| `git push -u origin main` | Push commits and set upstream tracking branch |

> **Tip:** `-u` (`--set-upstream`) is only needed on the first push. After that, `git push` is enough.

---

## 5. Full Sequence (Quick Reference)

```bash
mkdir <project> && cd <project>
git init
# --- add your files ---
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin <url>
git push -u origin main
```
