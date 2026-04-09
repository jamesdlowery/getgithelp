# Git Cheat Sheets
> Linux · A complete reference from first-time setup to production

---

## Table of Contents

- [Part 0 — First-time GitHub setup](#part-0--first-time-github-setup)
- [Part 1 — Create a new repository](#part-1--create-a-new-repository)
- [Part 2 — Clone an existing repository](#part-2--clone-an-existing-repository)
- [Part 3 — Sync local changes](#part-3--sync-local-changes)
- [Part 4 — Merge to dev via PR](#part-4--merge-to-dev-via-pr)
- [Part 5 — Promote dev to main](#part-5--promote-dev-to-main)

---

## Legend

| Symbol | Meaning |
|---|---|
| `[local]` | Command run in your terminal |
| `[web UI]` | Action taken in GitHub or GitLab |
| `[run once]` | One-time global setup |
| `[gh]` | Requires the GitHub CLI (`gh`) |
| `[DANGER]` | Production impact — proceed with care |

---

<br>

# Part 0 — First-Time GitHub Setup

> **Flow:** install git + gh → global config → GitHub account → authenticate (PAT or SSH) → credential store → verify

---

### 0.1 · Install Git `[local]`

| Command | Distro |
|---|---|
| `sudo apt update && sudo apt install -y git` | Debian / Ubuntu / Mint |
| `sudo dnf install -y git` | Fedora / RHEL / CentOS |
| `sudo pacman -S git` | Arch / Manjaro |
| `git --version` | Confirm installation and check version |

> **Tip:** Git 2.28+ is recommended. If your distro ships an older version, add the official Git PPA:
> ```bash
> sudo add-apt-repository ppa:git-core/ppa
> sudo apt update && sudo apt install -y git
> ```

---

### 0.1b · Install GitHub CLI (`gh`) `[local]`

> **What is `gh`?** The official GitHub CLI. It talks to the GitHub *platform* — creating repos, opening PRs, managing issues — things `git` itself knows nothing about. See the `git` vs `gh` notes in Part 1.

| Distro | Command |
|---|---|
| Debian / Ubuntu / Mint | `sudo apt install gh` |
| Fedora / RHEL | `sudo dnf install gh` |
| Arch / Manjaro | `sudo pacman -S github-cli` |
| Manual (any distro) | See [cli.github.com](https://cli.github.com) |

```bash
gh --version                  # confirm installation
gh auth login                 # authenticate gh with your GitHub account
                              # follow the interactive prompts (browser or PAT)
gh auth status                # verify gh is authenticated
```

> **Note:** `gh auth login` handles its own authentication separately from `git`'s credential helper. You still need to set up `git` auth (PAT or SSH key) for `git push` / `git pull` operations.

---

### 0.2 · Global identity & config `[run once]`

| Command | Description |
|---|---|
| `git config --global user.name "Your Name"` | Your display name — appears in all commits |
| `git config --global user.email "you@email.com"` | Must match your GitHub account email |
| `git config --global init.defaultBranch main` | Set default branch name to `main` for all new repos |
| `git config --global core.editor nano` | Set default editor for commit messages (nano, vim, etc.) |
| `git config --global pull.rebase true` | Default to rebase instead of merge on `git pull` |
| `git config --global color.ui auto` | Enable coloured terminal output |
| `git config --global --list` | Review all global settings |

> **Note:** Global config is stored in `~/.gitconfig`. You can also edit it directly with `nano ~/.gitconfig`.

---

### 0.3 · Create / configure GitHub account `[web UI]`

| Action | Why |
|---|---|
| Sign up or log in at github.com | Use the same email as `user.email` above |
| Settings → Emails → verify your email address | GitHub won't accept pushes from an unverified email |
| Settings → Profile → add name, avatar (optional) | Helps collaborators identify you in PRs and reviews |
| Settings → Security → enable two-factor authentication (2FA) | Required for most organisations; strongly recommended for all |

> **Warning:** GitHub no longer accepts plain passwords for Git operations. You must authenticate using a Personal Access Token (HTTPS) or an SSH key.

---

### 0.4 · Choose an authentication method

| Method | Best for |
|---|---|
| **Personal Access Token (PAT)** — HTTPS | Simpler setup; token acts as your password; works anywhere; best for getting started |
| **SSH Key** — SSH | More secure; no token to expire; password-free after setup; preferred for daily dev work |

> **Note:** You only need one method. Use PAT for simplicity; SSH for long-term use. Both are covered below.

---

### 0.5 · Option A — Personal Access Token (PAT) `[HTTPS]`

**Generate the token `[web UI]`**

| Action | Detail |
|---|---|
| GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic) → Generate new token | Set expiry; tick scopes: `repo`, `workflow` |
| Copy the token immediately | It is only shown once — store it in a password manager |

> **Tip:** Use fine-grained tokens for tighter per-repo scope. Classic tokens are simpler for general use.

**Store the token so Git remembers it `[local]`**

| Command | Description |
|---|---|
| `git config --global credential.helper store` | Save to `~/.git-credentials` (plaintext — trusted machines only) |
| `git config --global credential.helper 'cache --timeout=3600'` | Cache in memory for 1 hour (more secure) |
| `git config --global credential.helper /usr/share/doc/git/contrib/credential/libsecret/git-credential-libsecret` | Use GNOME Keyring / libsecret (most secure) |

> **Note:** After setting a credential helper, the first `git push` will prompt for username + token. Git stores it automatically after that.

---

### 0.6 · Option B — SSH key `[SSH]`

| Command | Description |
|---|---|
| `ssh-keygen -t ed25519 -C "you@email.com"` | Generate a new Ed25519 key pair (recommended algorithm) |
| `eval "$(ssh-agent -s)"` | Start the SSH agent in the current shell |
| `ssh-add ~/.ssh/id_ed25519` | Add your private key to the agent |
| `cat ~/.ssh/id_ed25519.pub` | Print your public key — copy the entire output |
| GitHub → Settings → SSH and GPG keys → New SSH key | Paste the public key, give it a title, save |

> **Tip:** Add this to `~/.ssh/config` so the agent loads your key automatically on login:
> ```
> Host github.com
>   AddKeysToAgent yes
>   IdentityFile ~/.ssh/id_ed25519
> ```

---

### 0.7 · Verify the connection `[local]`

| Command | Description |
|---|---|
| `ssh -T git@github.com` | Test SSH auth — expect: `Hi <username>! You've successfully authenticated` |
| `git config --global --list` | Confirm all global settings are correct |
| `git clone git@github.com:<user>/<repo>.git` | SSH clone — confirms end-to-end auth works |
| `git clone https://github.com/<user>/<repo>.git` | HTTPS clone — confirms PAT auth works |

> **Tip:** Run `ssh -vT git@github.com` for verbose output if the connection test fails — it will show exactly where authentication breaks down.

---

### 0.8 · `GH_TOKEN` — environment variable authentication

> **What is it?** An alternative to `gh auth login` for non-interactive environments. Set `GH_TOKEN` to a GitHub PAT and `gh` uses it automatically — no stored credentials needed. If both are present, `GH_TOKEN` takes precedence over `gh auth login`.

| | `gh auth login` | `GH_TOKEN` |
|---|---|---|
| How set | Interactive prompt | Environment variable |
| Persists across sessions | Yes — stored in `~/.config/gh/hosts.yml` | No — lives in memory only |
| Works without a human | No | Yes — fully scriptable |
| Best for | Developer workstations | CI/CD, scripts, Docker, automation |
| Configures `git` credential helper | Yes (HTTPS option) | No — `git push`/`pull` still needs separate auth |

**Session-only (current terminal):**
```bash
export GH_TOKEN=<your-PAT>
gh auth status                # verify it works
```

**Persistent in shell profile (personal machine):**
```bash
echo 'export GH_TOKEN=<your-PAT>' >> ~/.bashrc
source ~/.bashrc
```

> **Warning:** Never hard-code a real token in a script that gets committed to a repo. Use your CI/CD platform's secret injection instead (e.g. GitHub Actions `secrets.GITHUB_TOKEN`).

**In a GitHub Actions workflow:**
```yaml
- name: Run gh command
  env:
    GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}   # automatically provided by GitHub Actions
  run: gh pr list
```

**In a shell script:**
```bash
#!/bin/bash
# Token injected at runtime by CI — never hard-coded
gh repo list --json name,visibility \
  --jq '.[] | "\(.name) (\(.visibility))"'
```

> **Note:** `GH_TOKEN` authenticates `gh` only. For `git push` / `git pull` over HTTPS, set `GITHUB_TOKEN` (or your PAT) as the credential separately, or use SSH keys.

---

### 0.9 · Quick reference

```bash
# --- install git ---
sudo apt install -y git && git --version

# --- install gh ---
sudo apt install gh && gh --version
gh auth login                 # authenticate gh interactively

# --- global git config ---
git config --global user.name "Your Name"
git config --global user.email "you@email.com"
git config --global init.defaultBranch main
git config --global pull.rebase true

# --- SSH key for git (recommended) ---
ssh-keygen -t ed25519 -C "you@email.com"
eval "$(ssh-agent -s)" && ssh-add ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub     # paste into GitHub → Settings → SSH keys

# --- verify both ---
ssh -T git@github.com
gh auth status
```

---

<br>

# Part 1 — Create a New Repository

> **Two approaches:** use `gh` to create the remote repo in one step, or use `git` alone with a manually created remote.

---

### `git` vs `gh` — which to use?

| | `git` only | `gh` + `git` |
|---|---|---|
| Creates the remote repo on GitHub | No — must create manually via web UI or API | Yes — `gh repo create` does it from the terminal |
| Works with GitLab / other hosts | Yes | No — GitHub only |
| Requires `gh` installed | No | Yes |
| Best for | Any Git host | GitHub-only workflows |

> **Recommendation:** Use `gh` if you're working exclusively with GitHub. Use `git` alone if you need host-agnostic scripts or work across multiple platforms.

---

### 1.1 · Option A — Create with `gh` (GitHub only) `[gh]`

```bash
# Create remote repo + clone it locally in one command
gh repo create <project-name> --public --clone
cd <project-name>

# Or: create from an existing local folder
cd <existing-project>
gh repo create <project-name> --public --source=. --remote=origin --push
```

| `gh repo create` flag | Description |
|---|---|
| `--public` | Make the repo public (use `--private` for private) |
| `--clone` | Clone the new remote repo locally after creating it |
| `--source=.` | Use the current directory as the local source |
| `--remote=origin` | Set the new GitHub repo as the `origin` remote |
| `--push` | Push local commits to the remote immediately |
| `--description "text"` | Add a repo description |
| `--add-readme` | Initialise with a README on GitHub |
| `--gitignore <template>` | Add a `.gitignore` from a GitHub template (e.g. `Node`, `Python`) |

> **Tip:** `gh repo create` with `--clone` gives you a fully connected local + remote repo in a single command — no `git remote add` needed.

---

### 1.2 · Option B — Create with `git` only `[local]`

#### Create & initialize

| Command | Description |
|---|---|
| `mkdir <project-name>` | Create a new project folder |
| `cd <project-name>` | Navigate into that folder |
| `git init` | Initialize an empty Git repo (creates `.git/`) |

> **Tip:** You'll need to create the remote repo on GitHub (web UI or `curl` API call) before you can push.

#### Add content

| Command | Description |
|---|---|
| `touch README.md` | Create an empty README file |
| `echo "# My Project" >> README.md` | Append content to a file from the terminal |
| `git status` | See which files are untracked / modified |
| `git add <file>` | Stage a specific file |
| `git add .` | Stage *all* new and modified files in current directory |
| `git add -p` | Interactively stage specific hunks (partial add) |

> **Tip:** Create a `.gitignore` before `git add .` to exclude files like `node_modules/` or `.env`.

#### Initial commit

| Command | Description |
|---|---|
| `git commit -m "Initial commit"` | Commit staged files with a message |
| `git log --oneline` | Verify the commit was recorded |
| `git branch -M main` | Rename default branch to `main` (if needed) |

#### Connect remote & push

| Command | Description |
|---|---|
| `git remote add origin <url>` | Link local repo to a remote (GitHub, GitLab, etc.) |
| `git remote -v` | Verify the remote URL is set correctly |
| `git push -u origin main` | Push commits and set upstream tracking branch |

> **Tip:** `-u` (`--set-upstream`) is only needed on the first push. After that, `git push` is enough.

---

### 1.3 · Quick reference

**Using `gh` (fastest):**
```bash
gh repo create <project-name> --public --clone
cd <project-name>
touch README.md
git add . && git commit -m "Initial commit"
git push
```

**Using `git` only:**
```bash
mkdir <project> && cd <project>
git init
# --- create the remote repo on GitHub first, then: ---
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin <url>
git push -u origin main
```

---

<br>

# Part 2 — Clone an Existing Repository

> **Flow:** remote repo → `git clone` → local working copy → branch → develop

---

### 2.1 · Clone a repository `[local]`

| Command | Description |
|---|---|
| `git clone <url>` | Clone a repo into a new folder (folder named after repo) |
| `git clone <url> <directory>` | Clone into a specific folder name |
| `git clone --depth 1 <url>` | Shallow clone — latest snapshot only, no full history |
| `git clone -b <branch> <url>` | Clone and immediately check out a specific branch |

> **Tip:** Get the URL from the GitHub/GitLab repo page — use HTTPS for simplicity, SSH for key-based auth.

---

### 2.2 · Explore after cloning `[local]`

| Command | Description |
|---|---|
| `cd <project>` | Navigate into the cloned folder |
| `git log --oneline -10` | View the last 10 commits |
| `git branch -a` | List all local and remote branches |
| `git remote -v` | Confirm the remote URL (should be `origin`) |
| `git status` | Confirm you are on the default branch with a clean tree |

---

### 2.3 · Check out a branch `[local]`

| Command | Description |
|---|---|
| `git checkout <branch>` | Switch to an existing local branch |
| `git checkout -b <branch>` | Create and switch to a new branch |
| `git checkout -b <branch> origin/<branch>` | Create a local branch tracking an existing remote branch |
| `git switch <branch>` | Modern alternative to `git checkout <branch>` |
| `git switch -c <branch>` | Modern alternative to `git checkout -b <branch>` |

> **Tip:** Always create a new branch before making changes — never work directly on `main` or `dev`.

---

### 2.4 · Configure the clone (if needed) `[local]`

| Command | Description |
|---|---|
| `git config user.name "Your Name"` | Set name for this repo only |
| `git config user.email "you@email.com"` | Set email for this repo only |
| `git remote set-url origin <new-url>` | Change the remote URL (e.g. HTTPS → SSH) |
| `git remote add upstream <url>` | Add a second remote (e.g. original repo when working from a fork) |

> **Tip:** Use `--global` to apply settings to all repos, or omit it to apply to this repo only.

---

### 2.5 · Stay up to date `[local]`

| Command | Description |
|---|---|
| `git fetch origin` | Download remote changes without touching your working tree |
| `git pull` | Fetch and merge the tracked remote branch into current branch |
| `git pull --rebase` | Fetch and rebase local commits on top of remote (cleaner history) |
| `git pull origin <branch>` | Pull a specific remote branch into the current branch |

> **Tip:** Run `git fetch origin` regularly to stay aware of remote changes without committing to a merge.

---

### 2.6 · Quick reference

```bash
git clone <url>
cd <project>
git branch -a                          # see all available branches
git checkout -b <your-branch>          # create a working branch
# --- make your changes, then sync ---
git fetch origin                       # stay up to date
git pull --rebase                      # incorporate remote changes
```

---

<br>

# Part 3 — Sync Local Changes

> **Flow:** working dir → stage (`git add`) → commit → pull → push

---

### 3.1 · Check what has changed `[local]`

| Command | Description |
|---|---|
| `git status` | Overview of all tracked/untracked changes in working tree |
| `git status -s` | Short format — two-letter status codes per file |
| `git diff` | Show unstaged line-level changes |
| `git diff --staged` | Show staged changes (what will be committed) |

> **Status codes:** `??` untracked &nbsp;|&nbsp; `M` modified &nbsp;|&nbsp; `A` staged new &nbsp;|&nbsp; `D` deleted &nbsp;|&nbsp; `R` renamed

---

### 3.2 · Stage changes `[local]`

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

### 3.3 · Commit `[local]`

| Command | Description |
|---|---|
| `git commit -m "message"` | Commit staged changes with an inline message |
| `git commit -am "message"` | Stage all tracked changes and commit in one step (skips `git add`) |
| `git commit --amend` | Modify the most recent commit (message or staged content) |

> **Warning:** `--amend` rewrites history — only use it before pushing, never on commits already on the remote.

---

### 3.4 · Pull before you push `[local]`

| Command | Description |
|---|---|
| `git fetch origin` | Download remote changes without merging |
| `git pull` | Fetch and merge remote branch into current branch |
| `git pull --rebase` | Fetch and rebase local commits on top of remote (cleaner history) |

> **Tip:** Always pull before pushing to avoid rejected pushes due to diverged history.

---

### 3.5 · Push to remote `[local]`

| Command | Description |
|---|---|
| `git push` | Push committed changes to the tracked remote branch |
| `git push origin <branch>` | Push to a specific branch on the remote |
| `git push --tags` | Push all local tags to the remote |

---

### 3.6 · Undo & recover `[local]`

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

### 3.7 · Quick reference

```bash
git status
git add -A                            # stage new + changed + deleted
git commit -m "describe your changes"
git pull --rebase                     # sync remote changes first
git push                              # send to remote
```

---

<br>

# Part 4 — Merge to Dev via PR

> **Flow:** feature branch → push → open PR → review & approve → merge PR → sync local dev

---

### 4.1 · Clean up & push your branch `[local]`

| Command | Description |
|---|---|
| `git status` | Confirm no uncommitted changes remain |
| `git push -u origin <your-branch>` | Push branch to remote and set upstream tracking |

> **Note:** The `-u` flag is only needed the first time. Subsequent pushes just need `git push`.

---

### 4.2 · Open a pull request `[web UI]`

| Platform | Navigation |
|---|---|
| **GitHub** | Repo → Pull Requests → New pull request → base: `dev` / compare: `<your-branch>` |
| **GitLab** | Repo → Merge Requests → New merge request → source: `<your-branch>` / target: `dev` |

> **Tip:** Write a clear PR title and description — what changed, why, and any testing notes.
> Assign reviewers and link related issues.

---

### 4.3 · Address review feedback `[local]`

| Command | Description |
|---|---|
| `git add -A && git commit -m "address review feedback"` | Commit changes requested by reviewers |
| `git push` | Push updates — PR refreshes automatically |

> **Note:** Repeat commit + push as many times as needed. The PR always reflects the latest state of your branch.

---

### 4.4 · Resolve conflicts (if flagged in the PR) `[local]`

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

### 4.5 · Merge the PR `[web UI]`

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

### 4.6 · Sync local dev after the merge `[local]`

| Command | Description |
|---|---|
| `git checkout dev` | Switch to local dev branch |
| `git pull origin dev` | Pull the merged state from remote into local dev |

---

### 4.7 · Clean up `[local]`

| Command | Description |
|---|---|
| `git branch -d <your-branch>` | Delete local branch (platform may auto-delete the remote branch) |
| `git push origin --delete <your-branch>` | Delete remote branch if not auto-deleted by the platform |
| `git fetch --prune` | Remove stale remote-tracking references locally |

> **Note:** Use `-D` instead of `-d` to force-delete a branch not yet fully merged (use with care).

---

### 4.8 · Quick reference

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

---

<br>

# Part 5 — Promote Dev to Main

> **Flow:** dev (fully tested) → release PR → review & approve → merge to main → tag → sync dev

---

### 5.1 · Pre-flight checks `[local]`

| Command | Description |
|---|---|
| `git checkout dev && git pull origin dev` | Ensure local dev is fully up to date |
| `git log origin/main..dev --oneline` | List all commits on dev not yet in main — review before proceeding |
| `git diff origin/main..dev --stat` | Summary of files changed between dev and main |
| `git status` | Confirm dev is clean with no uncommitted changes |

> **Warning:** Never promote a dev branch that has failing tests, uncommitted changes, or unreviewed commits.

---

### 5.2 · Open a release PR: dev → main `[web UI]`

| Platform | Navigation |
|---|---|
| **GitHub** | Pull Requests → New pull request → base: `main` / compare: `dev` |
| **GitLab** | Merge Requests → New merge request → source: `dev` / target: `main` |

> **Tip:** Title the PR clearly, e.g. `Release v1.2.0`. Include a changelog or summary of what's shipping.
> Assign senior reviewers — this is going to production.

---

### 5.3 · Review, approve & merge the PR `[web UI]`

| Action | Description |
|---|---|
| Reviewers approve the PR | At least one (preferably two) senior approvals required |
| All CI checks pass | Tests, linting, security scans — nothing merges red |
| Click "Merge pull request" → "Create a merge commit" | Use a merge commit to preserve the full dev history on main |

> **Warning:** Do not use squash or rebase merge strategies for a dev → main promotion.
> A merge commit clearly marks the release point in the history.

---

### 5.4 · Tag the release `[local]`

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

### 5.5 · Sync dev with main post-merge `[local]`

| Command | Description |
|---|---|
| `git checkout dev` | Switch back to dev |
| `git merge main` | Bring the merge commit back into dev to keep histories aligned |
| `git push origin dev` | Push the updated dev to remote |

> **Note:** Skipping this step causes dev to diverge from main over time, leading to messy future merges.

---

### 5.6 · Emergency rollback `[DANGER]`

> **Warning:** Always prefer `git revert` — it is safe and non-destructive. Only use
> `reset --hard` + force-push as an absolute last resort with full team awareness.

| Command | Description |
|---|---|
| `git revert HEAD && git push origin main` | **Safe** — creates a new revert commit, preserves full history |
| `git checkout main && git reset --hard <prev-tag>` | **Destructive** — hard reset to a previous tag |
| `git push --force-with-lease origin main` | Force-push after hard reset — only if branch protections allow it |

---

### 5.7 · Quick reference

```bash
# --- local: pre-flight ---
git checkout dev && git pull origin dev
git log origin/main..dev --oneline      # review commits shipping to prod

# --- web UI: open PR dev → main, get approvals, all CI green, merge ---

# --- local: tag the release ---
git checkout main && git pull origin main
git tag -a v1.2.0 -m "Release v1.2.0"
git push origin v1.2.0

# --- local: sync dev with main ---
git checkout dev
git merge main && git push origin dev
```

---

*Generated for Linux. For the latest Git docs visit [git-scm.com](https://git-scm.com/doc).*
