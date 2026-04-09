# Git — Sync Local Changes Cheat Sheet
> Linux · status · stage · commit · push

---

## 1. Check What Has Changed

| Command | Description |
|---|---|
| `git status` | Overview of all tracked/untracked changes in working tree |
| `git status -s` | Short format — two-letter status codes per file |
| `git diff` | Show unstaged line-level changes |
| `git diff --staged` | Show staged changes (what will be committed) |

> **Status codes:** `??` untracked &nbsp;|&nbsp; `M` modified &nbsp;|&nbsp; `A` staged new &nbsp;|&nbsp; `D` deleted &nbsp;|&nbsp; `R` renamed

---

## 2. Stage Changes

| Command | Type | Description |
|---|---|---|
| `git add .` | new, changed | Stage all new and modified files recursively |
| `git add <file>` | new, changed | Stage a specific file |
| `git add <directory>/` | new, changed | Stage all changes inside a directory |
| `git rm <file>` | deleted | Stage a deletion and remove file from disk |
| `git rm --cached <file>` | deleted | Untrack a file but keep it on disk |
| `git mv <old> <new>` | moved | Rename or move a file and stage it atomically |
| `git add -A` | new, changed, deleted | Stage everything — new, modified, and deleted |
| `git add -p` | — | Interactively choose which hunks to stage |

> **Note:** `git add .` stages new + modified but NOT deletions made with plain `rm`.
> Use `git add -A` to catch those too.

---

## 3. Commit

| Command | Description |
|---|---|
| `git commit -m "message"` | Commit staged changes with an inline message |
| `git commit -am "message"` | Stage all tracked changes and commit in one step (skips `git add`) |
| `git commit --amend` | Modify the most recent commit (message or staged content) |

> **Warning:** `--amend` rewrites history — only use it before pushing, never on commits already on the remote.

---

## 4. Pull Before You Push

| Command | Description |
|---|---|
| `git fetch origin` | Download remote changes without merging |
| `git pull` | Fetch and merge remote branch into current branch |
| `git pull --rebase` | Fetch and rebase local commits on top of remote (cleaner history) |

> **Tip:** Always pull before pushing to avoid rejected pushes due to diverged history.

---

## 5. Push to Remote

| Command | Description |
|---|---|
| `git push` | Push committed changes to the tracked remote branch |
| `git push origin <branch>` | Push to a specific branch on the remote |
| `git push --tags` | Push all local tags to the remote |

---

## 6. Undo & Recover

| Command | Description |
|---|---|
| `git restore <file>` | Discard unstaged changes to a file (restores from last commit) |
| `git restore --staged <file>` | Unstage a file (keeps changes in working tree) |
| `git stash` | Temporarily shelve all uncommitted changes |
| `git stash pop` | Re-apply the most recently stashed changes |
| `git revert <commit>` | Create a new commit that undoes a previous one (safe for shared branches) |

> **Warning:** `git restore` permanently discards local changes — there is no undo.
> Use `git stash` instead if you are unsure.

---

## 7. Typical Sync Sequence (Quick Reference)

```bash
git status
git add -A                        # stage new + changed + deleted
git commit -m "describe your changes"
git pull --rebase                  # sync remote changes first
git push                           # send to remote
```
