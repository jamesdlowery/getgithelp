# Git & GitHub: A Complete Field Guide for Linux Developers

> **Audience:** Developers working on Debian/Ubuntu Linux using Bash who want to manage projects on GitHub entirely from the terminal using `git` and the `gh` CLI.  
> **Scope:** Repository creation → branching strategy → daily workflow → issue and PR management → promotions → reversions → GitHub Actions automation for Python software packaging (with trial/nag-screen and registration).

---

## Table of Contents

1. [Prerequisites & First-Time Setup](#1-prerequisites--first-time-setup)
2. [Core Concepts You Must Know](#2-core-concepts-you-must-know)
3. [Creating a New Repository](#3-creating-a-new-repository)
4. [Branching Strategy: dev → test → prod](#4-branching-strategy-dev--test--prod)
5. [Initializing the Repo & First Commit](#5-initializing-the-repo--first-commit)
6. [Daily Development Workflow](#6-daily-development-workflow)
7. [Issues: Tracking Work](#7-issues-tracking-work)
8. [Pull Requests: Promoting Changes](#8-pull-requests-promoting-changes)
9. [Promoting dev → test → prod](#9-promoting-dev--test--prod)
10. [Reverting Things](#10-reverting-things)
11. [GitHub Actions: Automating Python Software Packaging](#11-github-actions-automating-python-software-packaging)
12. [Quick Reference Cheat Sheet](#12-quick-reference-cheat-sheet)
13. [Glossary](#13-glossary)

---

## 1. Prerequisites & First-Time Setup

### 1.1 Install `git` and the `gh` CLI

```bash
# Update package list
sudo apt update

# Install git
sudo apt install -y git

# Install GitHub CLI (gh) — add the official repo first
type -p curl >/dev/null || sudo apt install curl -y
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg \
  | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] \
  https://cli.github.com/packages stable main" \
  | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
sudo apt update
sudo apt install -y gh

# Verify
git --version
gh --version
```

### 1.2 Configure `git` Identity (run once)

```bash
git config --global user.name  "Your Name"
git config --global user.email "you@example.com"
git config --global core.editor "nano"          # or vim, code, etc.
git config --global init.defaultBranch main     # modern default
```

### 1.3 Authenticate `gh` to GitHub

**Interactive (browser-based) — easiest:**

```bash
gh auth login
# Choose: GitHub.com → HTTPS → Login with a web browser
# Follow the one-time code prompt
```

**Token-based (for servers/scripts):**

```bash
# Generate a token at: https://github.com/settings/tokens
# Give it: repo, workflow, read:org scopes
export GH_TOKEN="ghp_yourTokenHere"

# To persist across sessions:
echo 'export GH_TOKEN="ghp_yourTokenHere"' >> ~/.bashrc
source ~/.bashrc
```

> **`git` vs `gh` — what's the difference?**  
> `git` is the core version-control tool that works entirely locally (commits, branches, merges).  
> `gh` is the GitHub CLI — it talks to the GitHub API to manage repos, PRs, issues, and Actions from your terminal. You need both.

---

## 2. Core Concepts You Must Know

| Concept | What it means |
|---|---|
| **Repository (repo)** | A folder tracked by git. Contains all your code and its full history. |
| **Commit** | A saved snapshot of your changes. Think of it as a named save point. |
| **Branch** | A parallel version of the code. Changes on one branch don't affect others until merged. |
| **Remote** | A copy of the repo hosted somewhere else (GitHub). `origin` is the conventional name for it. |
| **Push** | Send your local commits up to the remote (GitHub). |
| **Pull** | Download remote commits to your local machine. |
| **Fetch** | Download remote metadata without merging. |
| **Merge** | Combine the history of two branches into one. |
| **Pull Request (PR)** | A GitHub feature to review and merge one branch into another. Preferred over direct merging. |
| **Issue** | A GitHub task/bug/feature request. Can be linked to commits and PRs. |
| **HEAD** | A pointer to your current position in the commit history. |
| **Staging Area** | A holding zone where you queue up changes before committing them (`git add`). |

---

## 3. Creating a New Repository

### Option A — Create on GitHub first, then clone (recommended)

```bash
# Create a private repo on GitHub and clone it locally in one command
gh repo create my-project \
  --private \
  --description "My awesome project" \
  --clone

cd my-project
```

**Common flags for `gh repo create`:**

| Flag | Effect |
|---|---|
| `--public` | Make the repo publicly visible |
| `--private` | Make the repo private (default for new work) |
| `--clone` | Clone it to the current directory immediately |
| `--add-readme` | Add a starter README.md |
| `--gitignore Python` | Add a Python-specific .gitignore |
| `--license MIT` | Add an MIT license file |

```bash
# Full example with extras
gh repo create my-python-app \
  --private \
  --description "Python app with trial/registration" \
  --gitignore Python \
  --license MIT \
  --clone

cd my-python-app
```

### Option B — Initialize locally, push to GitHub

```bash
mkdir my-project && cd my-project
git init
# ... add files, make first commit (see Section 5) ...
gh repo create my-project --private --source=. --push
```

### Useful repo management commands

```bash
gh repo list                        # List your repos
gh repo view                        # View current repo info
gh repo view --web                  # Open repo in browser
gh repo delete my-project           # Delete a repo (irreversible!)
```

---

## 4. Branching Strategy: dev → test → prod

This guide uses a **three-branch promotion model**:

```
prod   ← stable, released code. Never commit here directly.
  ↑
test   ← integration/QA branch. Code that passed dev review.
  ↑
dev    ← active development. All feature work starts here.
```

### 4.1 Create the branches

```bash
# After cloning or initializing (you'll be on 'main' or 'master')
# Rename the default branch to 'prod' (the stable/release branch)
git branch -m main prod
git push -u origin prod

# Set prod as the default branch on GitHub
gh repo edit --default-branch prod

# Create 'test' from prod
git checkout -b test
git push -u origin test

# Create 'dev' from test
git checkout -b dev
git push -u origin dev

# Protect prod and test branches from direct pushes
gh api repos/{owner}/{repo}/branches/prod/protection \
  --method PUT \
  --field required_pull_request_reviews='{"required_approving_review_count":1}' \
  --field enforce_admins=true

# Simpler protection via web UI: Settings → Branches → Add rule
```

> **Branch protection** prevents anyone (including you) from pushing directly to `prod` or `test`. All changes must come through a reviewed Pull Request. This is a best practice even for solo projects.

### 4.2 Daily branch rule

```
All new work → branch off dev → PR back into dev → PR into test → PR into prod
```

Never commit directly to `test` or `prod`.

---

## 5. Initializing the Repo & First Commit

```bash
cd my-project    # Must be inside the repo directory

# Check current status (always a safe command — it changes nothing)
git status

# Create a .gitignore to exclude files git should never track
cat > .gitignore << 'EOF'
__pycache__/
*.py[cod]
*.egg-info/
dist/
build/
.env
*.env
venv/
.venv/
*.log
*.sqlite3
.DS_Store
EOF

# Create a starter README if you don't have one
echo "# My Project" > README.md

# Stage all new files (add them to the staging area)
git add .

# Verify what's staged
git status

# Make the first commit
git commit -m "Initial commit: add README and .gitignore"

# Push to the remote (GitHub) — sets upstream tracking
git push -u origin dev
```

### Understanding `git add`

```bash
git add .                  # Stage ALL changed/new files in current directory
git add filename.py        # Stage a single file
git add src/               # Stage an entire directory
git add -p                 # Interactive: review each change chunk before staging
git restore --staged file  # UN-stage a file (does not delete the file)
```

---

## 6. Daily Development Workflow

This is the loop you'll repeat every working session:

```bash
# 1. Switch to dev and pull latest changes
git checkout dev
git pull origin dev

# 2. (Optional but recommended) Create a feature branch for your work
git checkout -b feature/my-new-thing

# 3. Write code... edit files...

# 4. Check what changed
git status
git diff                   # Shows unstaged changes line by line
git diff --staged          # Shows staged changes

# 5. Stage your changes
git add .

# 6. Commit with a meaningful message
git commit -m "feat: add login validation to user module"

# 7. Push your branch to GitHub
git push -u origin feature/my-new-thing

# 8. Open a PR to merge feature branch → dev
gh pr create \
  --base dev \
  --head feature/my-new-thing \
  --title "Add login validation" \
  --body "Closes #12. Adds input sanitization to the login form."
```

### Commit message conventions (recommended)

```
feat:     A new feature
fix:      A bug fix
docs:     Documentation only changes
style:    Formatting, whitespace (no logic change)
refactor: Code restructuring (no feature/fix)
test:     Adding or fixing tests
chore:    Build process, dependency updates
```

Examples:
```
feat: add user registration endpoint
fix: resolve null pointer in payment processor
docs: update README with installation steps
chore: upgrade requests library to 2.31.0
```

### Keeping your branch up to date with dev

```bash
# While on your feature branch:
git fetch origin
git rebase origin/dev      # Preferred: keeps history linear
# OR
git merge origin/dev       # Alternative: creates a merge commit
```

---

## 7. Issues: Tracking Work

Issues are GitHub's built-in task tracker. Link them to commits and PRs for a full audit trail.

### Create an issue

```bash
# Interactive
gh issue create

# One-liner
gh issue create \
  --title "Bug: login fails with special characters" \
  --body "Steps to reproduce: enter '@' in password field. Expected: login succeeds." \
  --label "bug" \
  --assignee "@me"
```

### Manage issues

```bash
gh issue list                        # List open issues
gh issue list --state closed         # List closed issues
gh issue list --label "bug"          # Filter by label
gh issue view 12                     # View issue #12
gh issue close 12                    # Close issue #12
gh issue reopen 12                   # Reopen issue #12
gh issue comment 12 --body "Fixed in PR #15"
```

### Link issues to commits and PRs

In commit messages or PR bodies, use these magic keywords — GitHub automatically closes the issue when the PR merges:

```
Closes #12
Fixes #12
Resolves #12
```

```bash
git commit -m "fix: handle special chars in password field — Closes #12"
```

### Create and manage labels

```bash
gh label list
gh label create "needs-testing" --color "#e11d48" --description "Requires QA"
```

---

## 8. Pull Requests: Promoting Changes

A Pull Request (PR) is a formal request to merge one branch into another. It's the right way to move code between branches, even when working solo.

### Create a PR

```bash
# Push branch first
git push -u origin feature/my-thing

# Create PR
gh pr create \
  --base dev \
  --head feature/my-thing \
  --title "Add thing" \
  --body "Description of changes. Closes #5."

# Create as a draft (not ready for review)
gh pr create --draft --base dev --head feature/my-thing --title "WIP: thing"
```

### Review and manage PRs

```bash
gh pr list                           # List open PRs
gh pr view 7                         # View PR #7
gh pr view 7 --web                   # Open PR in browser
gh pr diff 7                         # Show the diff for PR #7
gh pr review 7 --approve             # Approve PR #7
gh pr review 7 --request-changes --body "Please add tests"
gh pr comment 7 --body "LGTM!"
```

### Merge a PR

```bash
# Merge (creates merge commit)
gh pr merge 7 --merge

# Squash and merge (combines all commits into one — cleaner history)
gh pr merge 7 --squash

# Rebase and merge (linear history, no merge commit)
gh pr merge 7 --rebase

# Delete the branch after merging (recommended)
gh pr merge 7 --squash --delete-branch
```

> **Which merge strategy to use?**  
> - **Squash** — best for feature branches with many "WIP" commits. Keeps the target branch history clean.  
> - **Merge commit** — preserves full feature branch history. Good when individual commits matter.  
> - **Rebase** — cleanest linear history. Requires confidence with git rebase.

---

## 9. Promoting dev → test → prod

Promotion is just a PR from one environment branch to the next. Never bypass the PR process.

### Promote dev → test

```bash
# Ensure dev is up to date
git checkout dev
git pull origin dev

# Create the promotion PR
gh pr create \
  --base test \
  --head dev \
  --title "Promote dev → test ($(date +%Y-%m-%d))" \
  --body "## Changes included
- feat: login validation (#12)
- fix: null pointer in payment (#14)

## Testing checklist
- [ ] Smoke test login flow
- [ ] Verify payment processing"

# After review/approval, merge
gh pr merge --squash --delete-branch=false   # Don't delete dev!
```

### Promote test → prod

```bash
git checkout test
git pull origin test

gh pr create \
  --base prod \
  --head test \
  --title "RELEASE: Promote test → prod ($(date +%Y-%m-%d))" \
  --body "## Release Notes
- Adds login validation
- Fixes payment null pointer

## Sign-off
- [ ] QA approved
- [ ] Stakeholder approved"

gh pr merge --merge --delete-branch=false
```

### Tag a release after promoting to prod

```bash
git checkout prod
git pull origin prod

git tag -a v1.2.0 -m "Release v1.2.0: login validation + payment fix"
git push origin v1.2.0

# Or create a GitHub Release
gh release create v1.2.0 \
  --title "v1.2.0 — Login & Payment fixes" \
  --notes "See CHANGELOG.md for details"
```

---

## 10. Reverting Things

Git provides multiple ways to undo work. Choose based on what you need to undo and whether it's been pushed.

### Undo overview

| Situation | Command | Danger level |
|---|---|---|
| Undo unstaged changes to a file | `git restore filename` | Safe |
| Undo staged changes (un-add) | `git restore --staged filename` | Safe |
| Amend the last commit message | `git commit --amend -m "new message"` | ⚠ Don't do if pushed |
| Undo last commit, keep changes staged | `git reset --soft HEAD~1` | ⚠ Don't do if pushed |
| Undo last commit, keep changes unstaged | `git reset HEAD~1` | ⚠ Don't do if pushed |
| Undo last commit, discard changes | `git reset --hard HEAD~1` | ⚠⚠ Destructive |
| Safely undo a pushed commit | `git revert <hash>` | Safe (adds new commit) |
| Revert a merged PR | `gh pr revert <number>` | Safe |

### Safe revert of a pushed commit (recommended)

```bash
# Find the commit hash
git log --oneline -10

# Create a new commit that undoes the changes from that commit
git revert abc1234

# Push the revert commit
git push origin dev
```

### Revert a merged PR on GitHub

```bash
# This creates a new PR that undoes all changes from the original PR
gh pr revert 23
```

### Emergency: roll back prod to a previous release tag

```bash
# See existing tags
git tag --list

# Create a revert PR: reset prod to v1.1.0
git checkout prod
git pull origin prod
git checkout -b hotfix/emergency-rollback

# Reset to the prior release tag
git reset --hard v1.1.0
git push origin hotfix/emergency-rollback --force-with-lease

# Open emergency PR
gh pr create \
  --base prod \
  --head hotfix/emergency-rollback \
  --title "EMERGENCY: Roll back to v1.1.0" \
  --body "Rolling back due to critical issue in v1.2.0"
```

### Stashing — save work without committing

```bash
git stash                   # Stash current uncommitted changes
git stash list              # See all stashes
git stash pop               # Re-apply the most recent stash
git stash drop              # Discard the most recent stash
git stash apply stash@{2}   # Apply a specific stash
```

---

## 11. GitHub Actions: Automating Python Software Packaging

GitHub Actions lets you define automated workflows that run in response to events (push, PR, release, manual trigger). For a Python application, you can automate:

- Running tests on every push
- Packaging the app on release
- Applying **code obfuscation** (PyArmor) to protect your source
- Building a **trial version** with a nag screen
- Enabling **registration** to unlock the full version
- Distributing installers or bundles

### 11.1 Workflow file basics

Workflows live in `.github/workflows/` as YAML files.

```bash
mkdir -p .github/workflows
```

### 11.2 Run tests on every push to dev

Create `.github/workflows/test.yml`:

```yaml
name: Run Tests

on:
  push:
    branches: [dev, test]
  pull_request:
    branches: [dev, test, prod]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Check out code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt
          pip install pytest

      - name: Run tests
        run: pytest tests/ -v
```

### 11.3 Code obfuscation with PyArmor

[PyArmor](https://pyarmor.readthedocs.io/) encrypts and obfuscates Python bytecode so your source logic cannot be easily read or copied.

**Install locally to test:**

```bash
pip install pyarmor
```

**Obfuscate manually:**

```bash
# Obfuscate your entire src/ directory
pyarmor gen src/

# Output lands in dist/
ls dist/
```

**In a GitHub Actions workflow** (runs on release):

Create `.github/workflows/build.yml`:

```yaml
name: Build & Package

on:
  release:
    types: [published]   # Triggers when you publish a GitHub Release
  workflow_dispatch:     # Also allows manual trigger from the UI

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"

      - name: Install dependencies
        run: |
          pip install pyarmor pyinstaller
          pip install -r requirements.txt

      - name: Obfuscate source code
        run: |
          pyarmor gen --output dist_obf/ src/

      - name: Bundle with PyInstaller
        run: |
          pyinstaller --onefile --name myapp dist_obf/main.py

      - name: Upload artifact
        uses: actions/upload-artifact@v4
        with:
          name: myapp-linux
          path: dist/myapp
```

### 11.4 Trial version with nag screen

The nag-screen and registration system is **application logic** that you build into your Python code. GitHub Actions just packages and distributes it. Here is a recommended pattern:

**`src/trial.py`** — trial/registration module:

```python
import os
import sys
import hashlib
import datetime

TRIAL_DAYS = 14
LICENSE_FILE = os.path.expanduser("~/.myapp/license.key")
INSTALL_DATE_FILE = os.path.expanduser("~/.myapp/install_date")

def get_machine_id():
    """Generate a hardware-based machine ID."""
    import uuid
    return str(uuid.getnode())   # MAC address as integer

def is_registered():
    """Check if a valid license key exists."""
    if not os.path.exists(LICENSE_FILE):
        return False
    with open(LICENSE_FILE) as f:
        key = f.read().strip()
    # Validate: key must be SHA-256(machine_id + secret_salt)
    secret_salt = "CHANGE_THIS_TO_SOMETHING_SECRET"
    machine_id = get_machine_id()
    expected = hashlib.sha256(f"{machine_id}{secret_salt}".encode()).hexdigest()
    return key == expected

def get_trial_days_remaining():
    """Return how many trial days are left; -1 if expired."""
    os.makedirs(os.path.dirname(INSTALL_DATE_FILE), exist_ok=True)
    if not os.path.exists(INSTALL_DATE_FILE):
        with open(INSTALL_DATE_FILE, "w") as f:
            f.write(str(datetime.date.today()))
    with open(INSTALL_DATE_FILE) as f:
        install_date = datetime.date.fromisoformat(f.read().strip())
    elapsed = (datetime.date.today() - install_date).days
    remaining = TRIAL_DAYS - elapsed
    return max(remaining, -1)

def check_and_show_nag():
    """
    Call this at app startup.
    - If registered: continue silently.
    - If in trial: show nag with days remaining.
    - If expired: block launch and show registration prompt.
    """
    if is_registered():
        return   # Full version — no nag

    days_left = get_trial_days_remaining()

    if days_left < 0:
        print("=" * 60)
        print("  Your 14-day trial has EXPIRED.")
        print("  To continue using MyApp, please purchase a license.")
        print("  Run:  myapp --register <your-license-key>")
        print("=" * 60)
        sys.exit(1)

    print(f"  [ MyApp — Trial Version: {days_left} day(s) remaining ]")
    print(f"  Register at: https://yoursite.com/buy")
    print()

def register(key):
    """Save a license key to disk."""
    os.makedirs(os.path.dirname(LICENSE_FILE), exist_ok=True)
    if is_registered():
        print("Already registered!")
        return
    with open(LICENSE_FILE, "w") as f:
        f.write(key.strip())
    if is_registered():
        print("Registration successful! Thank you.")
    else:
        print("Invalid license key. Please check and try again.")
```

**`src/main.py`** — entry point:

```python
import sys
from trial import check_and_show_nag, register

def main():
    if len(sys.argv) == 3 and sys.argv[1] == "--register":
        register(sys.argv[2])
        return
    check_and_show_nag()
    # --- Your actual application code starts here ---
    print("Welcome to MyApp!")

if __name__ == "__main__":
    main()
```

**Usage by end user:**

```bash
# Run normally (trial mode)
./myapp

# Register with a purchased key
./myapp --register abc123def456...
```

### 11.5 Generating and distributing license keys

Create a **private** key-generation script (never commit this to a public repo):

```python
# keygen.py  —  run locally or via a private GitHub Action
import hashlib, sys

SECRET_SALT = "CHANGE_THIS_TO_SOMETHING_SECRET"   # Must match trial.py

def generate_key(machine_id: str) -> str:
    return hashlib.sha256(f"{machine_id}{SECRET_SALT}".encode()).hexdigest()

if __name__ == "__main__":
    machine_id = sys.argv[1]
    print(generate_key(machine_id))
```

```bash
# Customer emails you their machine ID (printed by your app)
python keygen.py "their_machine_id_here"
# → Send the output hash to the customer as their license key
```

### 11.6 Full automated release workflow

This workflow fires when you publish a GitHub Release. It obfuscates, builds, and attaches the binary to the release.

Create `.github/workflows/release.yml`:

```yaml
name: Release Build

on:
  release:
    types: [published]

permissions:
  contents: write   # Needed to upload to the release

jobs:
  build-linux:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"

      - name: Install build tools
        run: |
          pip install pyarmor pyinstaller
          pip install -r requirements.txt

      - name: Obfuscate with PyArmor
        run: |
          pyarmor gen --output obfuscated/ src/

      - name: Package with PyInstaller
        run: |
          pyinstaller \
            --onefile \
            --name myapp \
            --distpath ./release_dist \
            obfuscated/main.py

      - name: Attach binary to GitHub Release
        uses: softprops/action-gh-release@v2
        with:
          files: release_dist/myapp
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

**Trigger the workflow:**

```bash
# Tag and create a release — this fires the workflow automatically
git tag -a v1.0.0 -m "First release"
git push origin v1.0.0

gh release create v1.0.0 \
  --title "MyApp v1.0.0" \
  --notes "Initial release with 14-day trial"
```

### 11.7 Using GitHub Secrets for sensitive values

Never hardcode API keys or your license salt in workflow files. Use GitHub Secrets:

```bash
# Store a secret via CLI
gh secret set LICENSE_SALT --body "your_secret_salt_value"
gh secret set PYARMOR_LICENSE --body "your_pyarmor_license"
```

Reference in workflow YAML:

```yaml
env:
  LICENSE_SALT: ${{ secrets.LICENSE_SALT }}
```

---

## 12. Quick Reference Cheat Sheet

### Essential daily commands

```bash
git status                            # What's changed?
git log --oneline -10                 # Last 10 commits
git diff                              # Unstaged changes
git diff --staged                     # Staged changes
git pull origin dev                   # Get latest from remote
git push origin dev                   # Send commits to remote
```

### Branch operations

```bash
git branch                            # List local branches
git branch -a                         # List all branches (inc. remote)
git checkout dev                      # Switch to dev
git checkout -b feature/x             # Create and switch to new branch
git branch -d feature/x               # Delete local branch (safe)
git branch -D feature/x               # Force-delete local branch
git push origin --delete feature/x    # Delete remote branch
```

### Sync operations

```bash
git fetch origin                      # Download remote metadata
git pull --rebase origin dev          # Pull with rebase (clean history)
git merge origin/dev                  # Merge remote dev into current branch
```

### Undo operations

```bash
git restore filename                  # Discard unstaged changes
git restore --staged filename         # Unstage a file
git revert HEAD                       # Undo last commit (safe, adds commit)
git reset --soft HEAD~1               # Undo commit, keep changes staged
git reset --hard HEAD~1               # Undo commit AND discard changes ⚠
```

### `gh` operations

```bash
gh repo create myrepo --private --clone
gh pr create --base dev --title "My PR"
gh pr list
gh pr merge 7 --squash
gh issue create --title "Bug" --label bug
gh issue list
gh release create v1.0.0 --title "v1"
gh workflow list
gh run list
gh run view 12345
```

---

## 13. Glossary

| Term | Definition |
|---|---|
| **Branch** | An independent line of development within a repo. |
| **Checkout** | Switch to a different branch or commit. |
| **Cherry-pick** | Apply a single commit from one branch onto another. |
| **Clone** | Copy a remote repo to your local machine. |
| **Commit** | A saved snapshot of staged changes. |
| **Detached HEAD** | State where HEAD points to a commit rather than a branch. |
| **Fast-forward** | A merge where the target branch just moves forward to the source; no merge commit needed. |
| **Fetch** | Download remote objects and refs without merging. |
| **Fork** | A personal copy of someone else's repo on GitHub. |
| **gh** | The GitHub CLI tool for interacting with GitHub from the terminal. |
| **git** | The distributed version control system itself. |
| **HEAD** | A pointer to the currently checked-out commit or branch. |
| **Hook** | A script that runs automatically at specific git events (pre-commit, post-merge, etc.). |
| **Merge conflict** | When two branches changed the same part of a file and git can't auto-resolve. |
| **Origin** | The conventional name for the primary remote repository (usually on GitHub). |
| **PR (Pull Request)** | A GitHub mechanism to review and merge branches. |
| **Push** | Upload local commits to a remote repo. |
| **Rebase** | Replay commits from one branch onto another, creating a linear history. |
| **Remote** | A version of the repo hosted on a server (e.g., GitHub). |
| **Revert** | Create a new commit that undoes a previous commit (safe for shared history). |
| **Stash** | Temporarily shelve uncommitted changes without committing them. |
| **Tag** | A named reference to a specific commit, typically used for releases. |
| **Upstream** | The remote branch that a local branch tracks. |
| **Working Tree** | The actual files on disk that you edit. |

---

*This guide was written for Debian/Ubuntu Linux. All commands use Bash. For questions or corrections, open an issue in the repo.*
