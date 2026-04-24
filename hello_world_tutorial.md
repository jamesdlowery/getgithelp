# Hello, World! — GitHub Workflow Tutorial

> **Repository:** `hello_world` · **Releases:** 12 (v0.1–v1.6) · **Platforms:** Linux · Windows · macOS  
> **Builds:** GitHub Actions (PyInstaller) · **Branches:** dev → test → prod

A complete, professional GitHub workflow tutorial covering issues, branches, conventional commits, PR procedures, automated cross-platform builds via GitHub Actions, tagging, and GitHub Releases — across 12 incremental versions of a Python script compiled to native executables on Linux, Windows, and macOS.

---

## Release Map

| prod Tag | Greeting Added | Scope | Lines | Issue |
|----------|----------------|-------|-------|-------|
| `prod-v0.1` | Hello, God! | The Divine / Infinite | 1 | #1 |
| `prod-v0.2` | Hello, Universe! | All of existence | 2 | #2 |
| `prod-v0.3` | Hello, Local Group! | Our galaxy cluster | 3 | #3 |
| `prod-v0.4` | Hello, Milky Way! | Our galaxy | 4 | #4 |
| `prod-v0.5` | Hello, Solar System! | Our star system | 5 | #5 |
| **`prod-v1.0`** | **Hello, World! ★** | **Our planet — MAJOR RELEASE** | 6 | #6 |
| `prod-v1.1` | Hello, United States! | Country | 7 | #7 |
| `prod-v1.2` | Hello, Alabama! | State | 8 | #8 |
| `prod-v1.3` | Hello, Tuscaloosa County! | County | 9 | #9 |
| `prod-v1.4` | Hello, Northport! | City | 10 | #10 |
| `prod-v1.5` | Hello, Buckhead! | Neighborhood | 11 | #11 |
| **`prod-v1.6`** | **Hello, Foxtrail! 🏠** | **Street — FINAL RELEASE** | 12 | #12 |

---

## Table of Contents

- [§0 — Prerequisites & One-Time Setup](#0--prerequisites--one-time-setup)
- [§1 — Repository & Branch Structure](#1--repository--branch-structure)
- [§2 — GitHub Actions Workflows](#2--github-actions-workflows)
- [§3 — Issues & Project Planning](#3--issues--project-planning)
- [§4 — Commit Message Conventions](#4--commit-message-conventions)
- [§5 — The Repeating Workflow](#5--the-repeating-workflow)
- [§6 — Release v0.1 — Hello, God!](#6--release-v01--hello-god)
- [§7 — Release v0.2 — Hello, Universe!](#7--release-v02--hello-universe)
- [§8 — Release v0.3 — Hello, Local Group!](#8--release-v03--hello-local-group)
- [§9 — Release v0.4 — Hello, Milky Way!](#9--release-v04--hello-milky-way)
- [§10 — Release v0.5 — Hello, Solar System!](#10--release-v05--hello-solar-system)
- [§11 — Release v1.0 — Hello, World! ★](#11--release-v10--hello-world-)
- [§12 — Release v1.1 — Hello, United States!](#12--release-v11--hello-united-states)
- [§13 — Release v1.2 — Hello, Alabama!](#13--release-v12--hello-alabama)
- [§14 — Release v1.3 — Hello, Tuscaloosa County!](#14--release-v13--hello-tuscaloosa-county)
- [§15 — Release v1.4 — Hello, Northport!](#15--release-v14--hello-northport)
- [§16 — Release v1.5 — Hello, Buckhead!](#16--release-v15--hello-buckhead)
- [§17 — Release v1.6 — Hello, Foxtrail! 🏠](#17--release-v16--hello-foxtrail-)
- [§18 — PR Procedures Reference](#18--pr-procedures-reference)
- [§19 — Release Notes Reference](#19--release-notes-reference)
- [REF — Quick Cheat Sheet](#ref--quick-cheat-sheet)

---

## §0 — Prerequisites & One-Time Setup

### Install Git

🐧 **Linux (Debian/Ubuntu)**
```bash
sudo apt update && sudo apt install -y git
git --version
```

🪟 **Windows (PowerShell — Admin)**
```powershell
winget install --id Git.Git -e --source winget
# Reopen PowerShell: git --version
```

🍎 **macOS**
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
brew install git && git --version
```

### Install Python 3

🐧 **Linux**
```bash
sudo apt install -y python3 python3-pip && python3 --version
```

🪟 **Windows**
```powershell
winget install Python.Python.3
# Reopen terminal: python --version
```

🍎 **macOS**
```bash
brew install python && python3 --version
```

### Install PyInstaller

PyInstaller compiles your Python script into a standalone native executable — no Python installation needed to run it.

```bash
pip3 install pyinstaller   # Linux / macOS
pip  install pyinstaller   # Windows
pyinstaller --version
```

### Install the GitHub CLI (gh)

🐧 **Linux**
```bash
(type -p wget >/dev/null || (sudo apt update && sudo apt install wget -y)) \
  && sudo mkdir -p -m 755 /etc/apt/keyrings \
  && wget -qO- https://cli.github.com/packages/githubcli-archive-keyring.gpg \
     | sudo tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
  && sudo chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
  && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" \
     | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
  && sudo apt update && sudo apt install gh -y
gh --version
```

🪟 **Windows**
```powershell
winget install --id GitHub.cli && gh --version
```

🍎 **macOS**
```bash
brew install gh && gh --version
```

### Configure Git Identity — All Platforms, Run Once

```bash
git config --global user.name  "Your Name"
git config --global user.email "you@example.com"
git config --global core.autocrlf false
git config --global init.defaultBranch prod
```

### Authenticate with GitHub

**Option A — Interactive (recommended)**
```bash
gh auth login
# Select: GitHub.com → HTTPS → Login with a web browser
```

**Option B — Token environment variable**

🐧 Linux / 🍎 macOS — add to `~/.bashrc` or `~/.zshrc`:
```bash
export GH_TOKEN=ghp_YourPersonalAccessTokenHere
```

🪟 Windows PowerShell:
```powershell
$env:GH_TOKEN = "ghp_YourPersonalAccessTokenHere"
```

> **Required PAT scopes:** github.com → Settings → Developer settings → Personal access tokens → Tokens (classic).
> Enable: **repo**, **workflow**, **read:org**. Verify with `gh auth status`.

---

## §1 — Repository & Branch Structure

### Create the Repository and Clone It

```bash
gh repo create hello_world \
  --public \
  --description "Hello World GitHub workflow tutorial — v0.1 through v1.6" \
  --clone
cd hello_world
```

### Initial Commit on prod

🐧 Linux / 🍎 macOS:
```bash
echo "# hello_world" > README.md
git add README.md && git commit -m "chore: initial commit"
git push -u origin prod
```

🪟 Windows:
```powershell
"# hello_world" | Out-File README.md -Encoding utf8
git add README.md && git commit -m "chore: initial commit"
git push -u origin prod
```

### Create test and dev Branches

```bash
git checkout -b test && git push -u origin test
git checkout -b dev  && git push -u origin dev
```

### Create PR Templates

```bash
mkdir -p .github/PULL_REQUEST_TEMPLATE

cat > .github/PULL_REQUEST_TEMPLATE/dev_to_test.md << 'EOF'
## Promote to Test — Checklist

**Release:** <!-- e.g. v1.1 -->
**Issue:** refs #<!-- issue number -->

### Changes
- <!-- describe what was added -->

### Dev Build Verified
- [ ] Tagged `dev-vX.X` — build-dev workflow completed
- [ ] `hello_world_dev_vX.X_linux` tested
- [ ] `hello_world_dev_vX.X_windows.exe` tested
- [ ] `hello_world_dev_vX.X_macos` tested
EOF

cat > .github/PULL_REQUEST_TEMPLATE/test_to_prod.md << 'EOF'
## Promote to Prod — Release Checklist

**Release:** <!-- e.g. v1.1 -->
**Issue:** closes #<!-- issue number -->

### Test Build Verified
- [ ] Tagged `test-vX.X` — build-test workflow completed
- [ ] Draft Release created with test executables attached
- [ ] `hello_world_test_vX.X_linux` acceptance tested
- [ ] `hello_world_test_vX.X_windows.exe` acceptance tested
- [ ] `hello_world_test_vX.X_macos` acceptance tested
EOF
```

### Create Issue Template

```bash
mkdir -p .github/ISSUE_TEMPLATE

cat > .github/ISSUE_TEMPLATE/release.md << 'EOF'
---
name: Release
about: Track a planned hello_world release
title: 'Release vX.X — Hello, [Scope]!'
labels: release
---

**Version:** vX.X  
**Greeting:** `print("Hello, [Scope]!")`

## Checklist
- [ ] Dev: create file, test, tag dev-vX.X, test executables, PR dev→test
- [ ] Test: tag test-vX.X, acceptance test, PR test→prod
- [ ] Prod: tag prod-vX.X, publish release, sync branches, close issue
EOF
```

### Commit Templates and Protect Branches

```bash
git add .github/
git commit -m "chore: add PR and issue templates"
git push origin dev
```

In the GitHub web UI: **Settings → Branches → Add branch protection rule** — for both `prod` and `test`, enable:
*Require a pull request before merging*, *Require approvals (1)*, and *Require status checks to pass*.

---

## §2 — GitHub Actions Workflows

| Tag pushed | Workflow triggered | Executables produced | Stored as |
|------------|-------------------|---------------------|-----------|
| `dev-vX.X` | `build-dev.yml` | `hello_world_dev_vX.X_{linux,windows.exe,macos}` | Actions Artifacts |
| `test-vX.X` | `build-test.yml` | `hello_world_test_vX.X_{linux,windows.exe,macos}` | Draft GitHub Release |
| `prod-vX.X` | `build-prod.yml` | `hello_world_prod_vX.X_{linux,windows.exe,macos}` | Published GitHub Release |

### Create the Workflows Directory

```bash
mkdir -p .github/workflows
```

### build-dev.yml

```yaml
name: Build Dev Executables

on:
  push:
    tags:
      - 'dev-v*'          # triggers ONLY on dev-vX.X tags

jobs:
  build:
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        include:
          - os: ubuntu-latest
            platform: linux
            ext: ''
          - os: windows-latest
            platform: windows
            ext: '.exe'
          - os: macos-latest
            platform: macos
            ext: ''

    runs-on: ${{ matrix.os }}

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.12'

      - name: Install PyInstaller
        run: pip install pyinstaller

      - name: Extract version from tag
        shell: bash
        run: echo "VERSION=${GITHUB_REF_NAME#dev-}" >> $GITHUB_ENV

      - name: Build executable
        shell: bash
        run: |
          OUTNAME="hello_world_dev_${{ env.VERSION }}_${{ matrix.platform }}${{ matrix.ext }}"
          pyinstaller --onefile --name "$OUTNAME" hello_world_dev_${{ env.VERSION }}.py

      - name: Upload artifact
        uses: actions/upload-artifact@v4
        with:
          name: hello_world_dev_${{ env.VERSION }}_${{ matrix.platform }}
          path: dist/hello_world_dev_*
```

### build-test.yml

```yaml
name: Build Test Executables

on:
  push:
    tags:
      - 'test-v*'         # triggers ONLY on test-vX.X tags

jobs:
  build:
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        include:
          - os: ubuntu-latest
            platform: linux
            ext: ''
          - os: windows-latest
            platform: windows
            ext: '.exe'
          - os: macos-latest
            platform: macos
            ext: ''

    runs-on: ${{ matrix.os }}

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.12'

      - name: Install PyInstaller
        run: pip install pyinstaller

      - name: Extract version from tag
        shell: bash
        run: echo "VERSION=${GITHUB_REF_NAME#test-}" >> $GITHUB_ENV

      - name: Build executable
        shell: bash
        run: |
          OUTNAME="hello_world_test_${{ env.VERSION }}_${{ matrix.platform }}${{ matrix.ext }}"
          pyinstaller --onefile --name "$OUTNAME" hello_world_test_${{ env.VERSION }}.py

      - name: Upload to draft GitHub Release
        uses: softprops/action-gh-release@v2
        with:
          tag_name: ${{ github.ref_name }}
          name: TEST ${{ env.VERSION }} — Acceptance Testing Build
          draft: true
          prerelease: true
          files: dist/hello_world_test_*
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### build-prod.yml

```yaml
name: Build Prod Executables

on:
  push:
    tags:
      - 'prod-v*'         # triggers ONLY on prod-vX.X tags

jobs:
  build:
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        include:
          - os: ubuntu-latest
            platform: linux
            ext: ''
          - os: windows-latest
            platform: windows
            ext: '.exe'
          - os: macos-latest
            platform: macos
            ext: ''

    runs-on: ${{ matrix.os }}

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.12'

      - name: Install PyInstaller
        run: pip install pyinstaller

      - name: Extract version from tag
        shell: bash
        run: echo "VERSION=${GITHUB_REF_NAME#prod-}" >> $GITHUB_ENV

      - name: Build executable
        shell: bash
        run: |
          OUTNAME="hello_world_prod_${{ env.VERSION }}_${{ matrix.platform }}${{ matrix.ext }}"
          pyinstaller --onefile --name "$OUTNAME" hello_world_prod_${{ env.VERSION }}.py

      - name: Publish GitHub Release
        uses: softprops/action-gh-release@v2
        with:
          tag_name: ${{ github.ref_name }}
          name: ${{ env.VERSION }} — Hello World
          draft: false
          prerelease: false
          files: dist/hello_world_prod_*
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### Commit and Push the Workflows

```bash
git add .github/workflows/
git commit -m "chore: add build-dev, build-test, build-prod GitHub Actions workflows"
git push origin dev
```

> **File naming note:** The source file must match the expected name for its branch stage.
> For v1.1 on dev: `hello_world_dev_v1.1.py`. The workflow strips the branch prefix from the
> tag (`dev-v1.1` → `v1.1`) to construct file and executable names automatically.

---

## §3 — Issues & Project Planning

Open an issue for every planned release **before** touching any code.

### Opening a Release Issue

```bash
gh issue create \
  --title "Release v1.1 — Hello, United States!" \
  --label "release" \
  --body "**Version:** v1.1  **Greeting:** print(\"Hello, United States!\")

## Checklist
- [ ] Dev: create file, test, tag dev-v1.1, test executables, PR dev→test
- [ ] Test: tag test-v1.1, acceptance test, PR test→prod
- [ ] Prod: tag prod-v1.1, publish release, sync branches, close issue"
```

Note the assigned issue number (e.g., `#7`). Use it in commit messages and PR bodies.

### Linking Issues to Commits and PRs

| Location | Syntax | Effect |
|----------|--------|--------|
| Commit message | `refs #7` | Cross-link — issue stays open |
| PR body (dev→test) | `refs #7` | Cross-link — issue stays open |
| PR body (test→prod) | `closes #7` | Auto-closes issue when PR merges to prod |

> `closes #N` only auto-closes when the PR merges into the **default branch** (prod).
> Always put it in the **test→prod** PR body.

### Create Labels

```bash
gh label create "release"  --color "#0075ca" --description "Planned release"
gh label create "cosmic"   --color "#7057ff" --description "Cosmic phase v0.x"
gh label create "geo"      --color "#e4e669" --description "Geographic phase v1.x"
gh label create "major"    --color "#d93f0b" --description "Major release"
```

---

## §4 — Commit Message Conventions

### Format

```
<type>(<scope>): <short description — under 72 chars>

[optional body]

[optional footer: refs #N, closes #N]
```

### Types Used in This Project

| Type | When to Use | Example |
|------|-------------|---------|
| `feat` | New greeting / feature | `feat: add Hello United States (v1.1) refs #7` |
| `fix` | Correcting a bug or wrong output | `fix: correct spelling of Tuscaloosa` |
| `chore` | Non-code — workflows, templates, renames | `chore: rename to hello_world_test_v1.1.py` |
| `docs` | Documentation only | `docs: update README with v1.1 release info` |
| `ci` | CI/CD config changes | `ci: pin PyInstaller to 6.x in workflows` |
| `refactor` | Code change without new feature or bug fix | `refactor: extract greetings into a list` |

---

## §5 — The Repeating Workflow

Every one of the 12 releases follows this identical 14-step sequence:

```
open issue → dev (code + tag dev-vX.X → build → test executables)
  → PR dev→test → rename on test + tag test-vX.X → build → draft release
  → acceptance test → PR test→prod → rename on prod + tag prod-vX.X
  → publish release → sync branches → close issue
```

**Step 1** — Open a GitHub Issue
```bash
gh issue create --title "Release vX.X — Hello, [Scope]!" --label "release"
# Note the issue number: #N
```

**Step 2** — Switch to dev, pull, create the versioned source file
```bash
git checkout dev && git pull origin dev
# Create hello_world_dev_vX.X.py (copy prev version, add print line, update docstring)
```

**Step 3** — Test the Python script locally
```bash
python3 hello_world_dev_vX.X.py   # Linux / macOS
python  hello_world_dev_vX.X.py   # Windows
```

**Step 4** — Commit and push to dev
```bash
git add hello_world_dev_vX.X.py
git commit -m "feat: add Hello [Scope] (vX.X) refs #N"
git push origin dev
```

**Step 5** — Tag `dev-vX.X` and push — triggers the dev build
```bash
git tag -a dev-vX.X -m "Dev build vX.X — Hello [Scope]"
git push origin dev-vX.X
# ⚙ build-dev.yml fires on GitHub Actions
```

**Step 6** — Download dev artifacts and test all 3 platform executables
```bash
gh run list --workflow=build-dev.yml --limit=5
gh run download <run-id>

# Linux
chmod +x hello_world_dev_vX.X_linux && ./hello_world_dev_vX.X_linux

# Windows
.\hello_world_dev_vX.X_windows.exe

# macOS
chmod +x hello_world_dev_vX.X_macos && ./hello_world_dev_vX.X_macos

# Verify: correct output, correct number of lines, exit code 0
```

**Step 7** — Open PR: dev → test, get it reviewed, merge it
```bash
gh pr create \
  --base test --head dev \
  --title "Promote vX.X to test — Hello [Scope]" \
  --body "**Issue:** refs #N  All dev executables verified"
gh pr merge --merge --delete-branch=false
```

**Step 8** — Switch to test, rename file, push
```bash
git checkout test && git pull origin test
git mv hello_world_dev_vX.X.py hello_world_test_vX.X.py
git commit -m "chore: rename to hello_world_test_vX.X.py for test stage"
git push origin test
```

**Step 9** — Tag `test-vX.X` and push — triggers the test build (draft release)
```bash
git tag -a test-vX.X -m "Test build vX.X — Hello [Scope]"
git push origin test-vX.X
# ⚙ build-test.yml fires — draft GitHub Release created with test executables attached
```

**Step 10** — Download test executables from draft release — acceptance test all 3 platforms
```bash
gh release download test-vX.X --dir ./test-artifacts

# Linux
chmod +x test-artifacts/hello_world_test_vX.X_linux
./test-artifacts/hello_world_test_vX.X_linux

# Windows
.\test-artifacts\hello_world_test_vX.X_windows.exe

# macOS
chmod +x test-artifacts/hello_world_test_vX.X_macos
./test-artifacts/hello_world_test_vX.X_macos
```

**Step 11** — Open PR: test → prod, get it reviewed, merge it
```bash
gh pr create \
  --base prod --head test \
  --title "Release vX.X — Hello [Scope]" \
  --body "**Issue:** closes #N  All acceptance tests pass"
gh pr merge --merge --delete-branch=false
```

**Step 12** — Switch to prod, rename file, push
```bash
git checkout prod && git pull origin prod
git mv hello_world_test_vX.X.py hello_world_prod_vX.X.py
git commit -m "chore: rename to hello_world_prod_vX.X.py for prod release"
git push origin prod
```

**Step 13** — Tag `prod-vX.X` and push — triggers the prod build (published release)
```bash
git tag -a prod-vX.X -m "Release vX.X — Hello [Scope]"
git push origin prod-vX.X
# ⚙ build-prod.yml fires — GitHub Release published with 3 prod executables
```

**Step 14** — Sync branches and close issue
```bash
git checkout test && git merge prod && git push origin test
git checkout dev  && git merge prod && git push origin dev
git checkout dev
# Issue auto-closed by test→prod PR merge (closes #N) — verify and close manually if needed
```

---

## §6 — Release v0.1 — Hello, God!

*The very first commit — creating `hello_world_dev_v0.1.py` from scratch.*

**hello_world_dev_v0.1.py**
```python
#!/usr/bin/env python3
"""
hello_world_dev_v0.1.py
Dev build — v0.1
Cross-platform: Linux, Windows, macOS.
"""

def main():
    print("Hello, God!")

if __name__ == "__main__":
    main()
```

**Full Workflow:**
```bash
# 1. Open issue #1
gh issue create --title "Release v0.1 — Hello, God!" --label "release,cosmic"

# 2. Dev
git checkout dev && git pull origin dev
# Create hello_world_dev_v0.1.py (content above)
python3 hello_world_dev_v0.1.py    # verify: Hello, God!
git add hello_world_dev_v0.1.py
git commit -m "feat: add Hello God — initial hello_world_dev_v0.1.py refs #1"
git push origin dev
git tag -a dev-v0.1 -m "Dev build v0.1 — Hello God" && git push origin dev-v0.1
# ⚙ build-dev.yml fires

# 3. Test dev executables — verify: Hello, God! on all 3 platforms
gh run list --workflow=build-dev.yml --limit=3
gh run download <run-id>
chmod +x hello_world_dev_v0.1_linux && ./hello_world_dev_v0.1_linux    # Linux
.\hello_world_dev_v0.1_windows.exe                                      # Windows
chmod +x hello_world_dev_v0.1_macos && ./hello_world_dev_v0.1_macos    # macOS

# 4. PR dev → test, merge
gh pr create --base test --head dev \
  --title "Promote v0.1 to test — Hello God" \
  --body "**Issue:** refs #1  All dev executables verified"
gh pr merge --merge --delete-branch=false

# 5. Rename on test, tag test build
git checkout test && git pull origin test
git mv hello_world_dev_v0.1.py hello_world_test_v0.1.py
git commit -m "chore: rename to hello_world_test_v0.1.py" && git push origin test
git tag -a test-v0.1 -m "Test build v0.1" && git push origin test-v0.1
# ⚙ build-test.yml fires — draft release created

# 6. Acceptance test test executables
gh release download test-v0.1 --dir ./test-artifacts
# Run all 3 — verify: Hello, God!

# 7. PR test → prod, merge
gh pr create --base prod --head test \
  --title "Release v0.1 — Hello God" \
  --body "**Issue:** closes #1  All acceptance tests pass"
gh pr merge --merge --delete-branch=false

# 8. Rename on prod, tag prod release
git checkout prod && git pull origin prod
git mv hello_world_test_v0.1.py hello_world_prod_v0.1.py
git commit -m "chore: rename to hello_world_prod_v0.1.py" && git push origin prod
git tag -a prod-v0.1 -m "Release v0.1 — Hello God" && git push origin prod-v0.1
# ⚙ build-prod.yml fires — publishes GitHub Release

# 9. Sync branches
git checkout test && git merge prod && git push origin test
git checkout dev  && git merge prod && git push origin dev
git checkout dev
```

**v0.1 shipped!** `hello_world_prod_v0.1_linux` · `hello_world_prod_v0.1_windows.exe` · `hello_world_prod_v0.1_macos`

---

## §7 — Release v0.2 — Hello, Universe!

**hello_world_dev_v0.2.py**
```python
#!/usr/bin/env python3
"""hello_world_dev_v0.2.py — Dev build — v0.2"""

def main():
    print("Hello, God!")
    print("Hello, Universe!")

if __name__ == "__main__":
    main()
```

Follow the §5 template substituting `v0.2` / issue `#2` / scope `Universe`.

```bash
git commit -m "feat: add Hello Universe (v0.2) refs #2"
git tag -a dev-v0.2  -m "Dev build v0.2"  && git push origin dev-v0.2
git tag -a test-v0.2 -m "Test build v0.2" && git push origin test-v0.2
git tag -a prod-v0.2 -m "Release v0.2 — Hello Universe" && git push origin prod-v0.2
# PR bodies: refs #2 (dev→test)  closes #2 (test→prod)
# Verify: 2 lines on all executables at dev and test stages
```

**v0.2 shipped!**

---

## §8 — Release v0.3 — Hello, Local Group!

**hello_world_dev_v0.3.py**
```python
#!/usr/bin/env python3
"""hello_world_dev_v0.3.py — Dev build — v0.3"""

def main():
    print("Hello, God!")
    print("Hello, Universe!")
    print("Hello, Local Group!")

if __name__ == "__main__":
    main()
```

```bash
git commit -m "feat: add Hello Local Group (v0.3) refs #3"
git tag -a dev-v0.3  -m "Dev build v0.3"  && git push origin dev-v0.3
git tag -a test-v0.3 -m "Test build v0.3" && git push origin test-v0.3
git tag -a prod-v0.3 -m "Release v0.3 — Hello Local Group" && git push origin prod-v0.3
```

**v0.3 shipped!**

---

## §9 — Release v0.4 — Hello, Milky Way!

**hello_world_dev_v0.4.py**
```python
#!/usr/bin/env python3
"""hello_world_dev_v0.4.py — Dev build — v0.4"""

def main():
    print("Hello, God!")
    print("Hello, Universe!")
    print("Hello, Local Group!")
    print("Hello, Milky Way!")

if __name__ == "__main__":
    main()
```

```bash
git commit -m "feat: add Hello Milky Way (v0.4) refs #4"
git tag -a dev-v0.4  -m "Dev build v0.4"  && git push origin dev-v0.4
git tag -a test-v0.4 -m "Test build v0.4" && git push origin test-v0.4
git tag -a prod-v0.4 -m "Release v0.4 — Hello Milky Way" && git push origin prod-v0.4
```

**v0.4 shipped!**

---

## §10 — Release v0.5 — Hello, Solar System!

**hello_world_dev_v0.5.py**
```python
#!/usr/bin/env python3
"""hello_world_dev_v0.5.py — Dev build — v0.5"""

def main():
    print("Hello, God!")
    print("Hello, Universe!")
    print("Hello, Local Group!")
    print("Hello, Milky Way!")
    print("Hello, Solar System!")

if __name__ == "__main__":
    main()
```

```bash
git commit -m "feat: add Hello Solar System (v0.5) refs #5"
git tag -a dev-v0.5  -m "Dev build v0.5"  && git push origin dev-v0.5
git tag -a test-v0.5 -m "Test build v0.5" && git push origin test-v0.5
git tag -a prod-v0.5 -m "Release v0.5 — Hello Solar System" && git push origin prod-v0.5
```

**v0.5 shipped!** Cosmic phase complete. Next: the major release.

---

## §11 — Release v1.0 — Hello, World! ★

> **Why v1.0 here?** "Hello, World!" is the repository's namesake — the universal programming
> landmark. Version bumps from v0.5 to v1.0 to mark this as the centerpiece release.
> Pre-1.0 was the cosmic build-up; post-1.0 zooms geographically home.

**hello_world_dev_v1.0.py**
```python
#!/usr/bin/env python3
"""
hello_world_dev_v1.0.py
Dev build — v1.0 — The Hello World milestone release.
Cross-platform: Linux, Windows, macOS.
"""

def main():
    print("Hello, God!")
    print("Hello, Universe!")
    print("Hello, Local Group!")
    print("Hello, Milky Way!")
    print("Hello, Solar System!")
    print("Hello, World!")

if __name__ == "__main__":
    main()
```

**Full workflow — major release:**

```bash
# Open issue #6 with major label
gh issue create --title "Release v1.0 — Hello, World! MAJOR RELEASE" --label "release,major"

# Dev
git commit -m "feat: add Hello World — major v1.0 milestone refs #6"
git tag -a dev-v1.0 -m "Dev build v1.0 — Hello World MAJOR" && git push origin dev-v1.0
# Test all 3 dev executables — 6 lines each

# PR dev→test
gh pr create --base test --head dev \
  --title "Promote v1.0 to test — Hello World (MAJOR)" \
  --body "**Release:** v1.0  **Issue:** refs #6  All 6-line dev executables verified"
gh pr merge --merge --delete-branch=false

# Test
git checkout test && git pull origin test
git mv hello_world_dev_v1.0.py hello_world_test_v1.0.py
git commit -m "chore: rename to hello_world_test_v1.0.py" && git push origin test
git tag -a test-v1.0 -m "Test build v1.0" && git push origin test-v1.0
# Acceptance test all 3 executables — 6 lines each

# PR test→prod
gh pr create --base prod --head test \
  --title "Release v1.0 — Hello, World!" \
  --body "## v1.0 — Major Release  **Issue:** closes #6

Greetings: God, Universe, Local Group, Milky Way, Solar System, **World** (new)
Linux | Windows | macOS — all verified"
gh pr merge --merge --delete-branch=false

# Prod
git checkout prod && git pull origin prod
git mv hello_world_test_v1.0.py hello_world_prod_v1.0.py
git commit -m "chore: rename to hello_world_prod_v1.0.py" && git push origin prod
git tag -a prod-v1.0 -m "Release v1.0 — Hello, World! — major milestone"
git push origin prod-v1.0
# build-prod.yml fires — publishes GitHub Release

# Sync
git checkout test && git merge prod && git push origin test
git checkout dev  && git merge prod && git push origin dev
git checkout dev
```

**v1.0 shipped! Hello, World!** — The major milestone. Geographic releases begin next.

---

## §12 — Release v1.1 — Hello, United States!

**hello_world_dev_v1.1.py**
```python
#!/usr/bin/env python3
"""hello_world_dev_v1.1.py — Dev build — v1.1"""

def main():
    print("Hello, God!")
    print("Hello, Universe!")
    print("Hello, Local Group!")
    print("Hello, Milky Way!")
    print("Hello, Solar System!")
    print("Hello, World!")
    print("Hello, United States!")

if __name__ == "__main__":
    main()
```

```bash
git commit -m "feat: add Hello United States (v1.1) refs #7"
git tag -a dev-v1.1  -m "Dev build v1.1"  && git push origin dev-v1.1
git tag -a test-v1.1 -m "Test build v1.1" && git push origin test-v1.1
git tag -a prod-v1.1 -m "Release v1.1 — Hello United States" && git push origin prod-v1.1
# PR bodies: refs #7 (dev→test)  closes #7 (test→prod)
# Verify: 7 lines on all executables at dev and test stages
```

**v1.1 shipped!**

---

## §13 — Release v1.2 — Hello, Alabama!

**hello_world_dev_v1.2.py**
```python
#!/usr/bin/env python3
"""hello_world_dev_v1.2.py — Dev build — v1.2"""

def main():
    print("Hello, God!")
    print("Hello, Universe!")
    print("Hello, Local Group!")
    print("Hello, Milky Way!")
    print("Hello, Solar System!")
    print("Hello, World!")
    print("Hello, United States!")
    print("Hello, Alabama!")

if __name__ == "__main__":
    main()
```

```bash
git commit -m "feat: add Hello Alabama (v1.2) refs #8"
git tag -a dev-v1.2  -m "Dev build v1.2"  && git push origin dev-v1.2
git tag -a test-v1.2 -m "Test build v1.2" && git push origin test-v1.2
git tag -a prod-v1.2 -m "Release v1.2 — Hello Alabama" && git push origin prod-v1.2
```

**v1.2 shipped!**

---

## §14 — Release v1.3 — Hello, Tuscaloosa County!

**hello_world_dev_v1.3.py**
```python
#!/usr/bin/env python3
"""hello_world_dev_v1.3.py — Dev build — v1.3"""

def main():
    print("Hello, God!")
    print("Hello, Universe!")
    print("Hello, Local Group!")
    print("Hello, Milky Way!")
    print("Hello, Solar System!")
    print("Hello, World!")
    print("Hello, United States!")
    print("Hello, Alabama!")
    print("Hello, Tuscaloosa County!")

if __name__ == "__main__":
    main()
```

```bash
git commit -m "feat: add Hello Tuscaloosa County (v1.3) refs #9"
git tag -a dev-v1.3  -m "Dev build v1.3"  && git push origin dev-v1.3
git tag -a test-v1.3 -m "Test build v1.3" && git push origin test-v1.3
git tag -a prod-v1.3 -m "Release v1.3 — Hello Tuscaloosa County" && git push origin prod-v1.3
```

**v1.3 shipped!**

---

## §15 — Release v1.4 — Hello, Northport!

**hello_world_dev_v1.4.py**
```python
#!/usr/bin/env python3
"""hello_world_dev_v1.4.py — Dev build — v1.4"""

def main():
    print("Hello, God!")
    print("Hello, Universe!")
    print("Hello, Local Group!")
    print("Hello, Milky Way!")
    print("Hello, Solar System!")
    print("Hello, World!")
    print("Hello, United States!")
    print("Hello, Alabama!")
    print("Hello, Tuscaloosa County!")
    print("Hello, Northport!")

if __name__ == "__main__":
    main()
```

```bash
git commit -m "feat: add Hello Northport (v1.4) refs #10"
git tag -a dev-v1.4  -m "Dev build v1.4"  && git push origin dev-v1.4
git tag -a test-v1.4 -m "Test build v1.4" && git push origin test-v1.4
git tag -a prod-v1.4 -m "Release v1.4 — Hello Northport" && git push origin prod-v1.4
```

**v1.4 shipped!**

---

## §16 — Release v1.5 — Hello, Buckhead!

**hello_world_dev_v1.5.py**
```python
#!/usr/bin/env python3
"""hello_world_dev_v1.5.py — Dev build — v1.5"""

def main():
    print("Hello, God!")
    print("Hello, Universe!")
    print("Hello, Local Group!")
    print("Hello, Milky Way!")
    print("Hello, Solar System!")
    print("Hello, World!")
    print("Hello, United States!")
    print("Hello, Alabama!")
    print("Hello, Tuscaloosa County!")
    print("Hello, Northport!")
    print("Hello, Buckhead!")

if __name__ == "__main__":
    main()
```

```bash
git commit -m "feat: add Hello Buckhead (v1.5) refs #11"
git tag -a dev-v1.5  -m "Dev build v1.5"  && git push origin dev-v1.5
git tag -a test-v1.5 -m "Test build v1.5" && git push origin test-v1.5
git tag -a prod-v1.5 -m "Release v1.5 — Hello Buckhead" && git push origin prod-v1.5
```

**v1.5 shipped!** One release left — home.

---

## §17 — Release v1.6 — Hello, Foxtrail! 🏠

**hello_world_dev_v1.6.py — THE COMPLETE FINAL FILE**
```python
#!/usr/bin/env python3
"""
hello_world_dev_v1.6.py
Dev build — v1.6 — The complete Hello World program.
From the infinite to the intimate — from God to Foxtrail Lane.
Cross-platform: Linux, Windows, macOS.
"""

def main():
    print("Hello, God!")
    print("Hello, Universe!")
    print("Hello, Local Group!")
    print("Hello, Milky Way!")
    print("Hello, Solar System!")
    print("Hello, World!")
    print("Hello, United States!")
    print("Hello, Alabama!")
    print("Hello, Tuscaloosa County!")
    print("Hello, Northport!")
    print("Hello, Buckhead!")
    print("Hello, Foxtrail!")

if __name__ == "__main__":
    main()
```

**Final expected output — all 12 lines:**
```
Hello, God!
Hello, Universe!
Hello, Local Group!
Hello, Milky Way!
Hello, Solar System!
Hello, World!
Hello, United States!
Hello, Alabama!
Hello, Tuscaloosa County!
Hello, Northport!
Hello, Buckhead!
Hello, Foxtrail!
```

**Full workflow — final release:**

```bash
# Open issue #12
gh issue create --title "Release v1.6 — Hello, Foxtrail! FINAL" --label "release,geo"

# Dev
git commit -m "feat: add Hello Foxtrail — complete v1.6 feature set refs #12"
git tag -a dev-v1.6 -m "Dev build v1.6 — Final" && git push origin dev-v1.6
# Test all 3 dev executables — verify all 12 lines

# PR dev→test (final)
gh pr create --base test --head dev \
  --title "Promote v1.6 to test — Hello Foxtrail (FINAL)" \
  --body "**Issue:** refs #12  All 12 dev executable lines verified"
gh pr merge --merge --delete-branch=false

# Test
git checkout test && git pull origin test
git mv hello_world_dev_v1.6.py hello_world_test_v1.6.py
git commit -m "chore: rename to hello_world_test_v1.6.py" && git push origin test
git tag -a test-v1.6 -m "Test build v1.6 — Final" && git push origin test-v1.6
# Acceptance test all 3 test executables — verify all 12 lines

# PR test→prod (final)
gh pr create --base prod --head test \
  --title "Release v1.6 — Hello Foxtrail — FINAL RELEASE" \
  --body "## v1.6 — Final Release  **Issue:** closes #12

All 12 greetings complete:
God, Universe, Local Group, Milky Way, Solar System, World,
United States, Alabama, Tuscaloosa County, Northport, Buckhead, Foxtrail (new)
Linux | Windows | macOS — all verified"
gh pr merge --merge --delete-branch=false

# Prod (final)
git checkout prod && git pull origin prod
git mv hello_world_test_v1.6.py hello_world_prod_v1.6.py
git commit -m "chore: rename to hello_world_prod_v1.6.py — final prod release"
git push origin prod
git tag -a prod-v1.6 -m "Release v1.6 — Hello Foxtrail — The complete Hello World journey"
git push origin prod-v1.6
# build-prod.yml fires — publishes final GitHub Release

# Final sync
git checkout test && git merge prod && git push origin test
git checkout dev  && git merge prod && git push origin dev
echo "All branches synced. v1.6 is live. The journey is complete."
```

**v1.6 shipped — The Journey is Complete!**

From "Hello, God!" at the beginning of everything to Foxtrail Lane in Northport, Alabama —
12 releases, 12 greetings, 3 platform executables per release, every build automated via
GitHub Actions, every promotion via pull request, every issue tracked and closed.

---

## §18 — PR Procedures Reference

### PR Lifecycle

| Stage | Action | Notes |
|-------|--------|-------|
| Create | `gh pr create` or GitHub web UI | Use the appropriate template body |
| Draft | Mark as Draft while build/tests run | Prevents accidental early merge |
| Ready | Mark Ready for Review when all checks pass | Notifies reviewers |
| Review | Reviewer approves or requests changes | At least 1 approval required |
| Checks | GitHub Actions build must pass | Required status check |
| Merge | `gh pr merge --merge --delete-branch=false` | Merge commit preserves full history |

### Review Commands

```bash
gh pr list                              # view open PRs
gh pr view <number>                     # inspect a PR
gh pr review <number> --approve         --body "Looks good."
gh pr review <number> --request-changes --body "Please fix X."
gh pr review <number> --comment         --body "Minor nit: ..."
```

### Abandoning a PR

```bash
gh pr close <number> --comment "Closing — reason: ..."
# Do NOT delete the branch from the remote — keep it for reference
```

---

## §19 — Release Notes Reference

### Template

```markdown
## vX.X — Hello, [Scope]!

### What's New
- Added `print("Hello, [Scope]!")` — [one sentence about the scope]

### Usage
./hello_world_prod_vX.X_linux          # Linux
.\hello_world_prod_vX.X_windows.exe    # Windows
./hello_world_prod_vX.X_macos          # macOS

### Downloads
- hello_world_prod_vX.X_linux
- hello_world_prod_vX.X_windows.exe
- hello_world_prod_vX.X_macos

### Output (all lines in order)
Hello, God!
...
Hello, [Scope]!    <- new in this release

No external dependencies. No Python installation required.
```

### Do's and Don'ts

| Do | Don't |
|----|-------|
| State what changed, plainly | Use internal jargon |
| Show the new expected output | Assume the reader knows previous state |
| List all assets explicitly | Make users guess which file to download |
| Link to the closing issue | Leave the issue connection invisible |
| Flag breaking changes prominently | Bury them in a paragraph |

---

## REF — Quick Cheat Sheet

### One-Time Setup

| Action | Command |
|--------|---------|
| Configure identity | `git config --global user.name "Name" && git config --global user.email "e@mail.com"` |
| Auth gh CLI | `gh auth login` |
| Create + clone repo | `gh repo create hello_world --public --clone` |
| Create branch | `git checkout -b <branch> && git push -u origin <branch>` |
| Create labels | `gh label create "release" --color "#0075ca"` |

### Per-Release Workflow (14 steps)

| # | Action | Command / Note |
|---|--------|----------------|
| 1 | Open issue | `gh issue create --title "..." --label "release"` |
| 2 | Dev: create file | Copy prev version, add print line |
| 3 | Test script locally | `python3 hello_world_dev_vX.X.py` |
| 4 | Commit & push | `git commit -m "feat: ... refs #N" && git push origin dev` |
| 5 | Tag dev build | `git tag -a dev-vX.X -m "..." && git push origin dev-vX.X` |
| 6 | Test dev executables | `gh run download <run-id>` — run on all 3 platforms |
| 7 | PR dev→test + merge | `gh pr create --base test --head dev ...` |
| 8 | Rename on test | `git mv hello_world_dev_vX.X.py hello_world_test_vX.X.py` |
| 9 | Tag test build | `git tag -a test-vX.X -m "..." && git push origin test-vX.X` |
| 10 | Acceptance test | `gh release download test-vX.X --dir ./test-artifacts` |
| 11 | PR test→prod + merge | `gh pr create --base prod --head test ...` (body: `closes #N`) |
| 12 | Rename on prod | `git mv hello_world_test_vX.X.py hello_world_prod_vX.X.py` |
| 13 | Tag prod release | `git tag -a prod-vX.X -m "..." && git push origin prod-vX.X` |
| 14 | Sync branches | `git checkout test && git merge prod && git push origin test` (repeat for dev) |

### Tag Naming Convention

| Tag | Triggers | Produces | Stored as |
|-----|----------|----------|-----------|
| `dev-vX.X` | `build-dev.yml` | `hello_world_dev_vX.X_{linux,windows.exe,macos}` | Actions Artifacts |
| `test-vX.X` | `build-test.yml` | `hello_world_test_vX.X_{linux,windows.exe,macos}` | Draft GitHub Release |
| `prod-vX.X` | `build-prod.yml` | `hello_world_prod_vX.X_{linux,windows.exe,macos}` | Published GitHub Release |

### Release History

| prod Tag | Greeting Added | Scope | Lines | Issue |
|----------|----------------|-------|-------|-------|
| `prod-v0.1` | Hello, God! | The Divine / Infinite | 1 | #1 |
| `prod-v0.2` | Hello, Universe! | All of existence | 2 | #2 |
| `prod-v0.3` | Hello, Local Group! | Our galaxy cluster | 3 | #3 |
| `prod-v0.4` | Hello, Milky Way! | Our galaxy | 4 | #4 |
| `prod-v0.5` | Hello, Solar System! | Our star system | 5 | #5 |
| **`prod-v1.0`** | **Hello, World! ★** | **Our planet — MAJOR** | 6 | #6 |
| `prod-v1.1` | Hello, United States! | Country | 7 | #7 |
| `prod-v1.2` | Hello, Alabama! | State | 8 | #8 |
| `prod-v1.3` | Hello, Tuscaloosa County! | County | 9 | #9 |
| `prod-v1.4` | Hello, Northport! | City | 10 | #10 |
| `prod-v1.5` | Hello, Buckhead! | Neighborhood | 11 | #11 |
| **`prod-v1.6`** | **Hello, Foxtrail! 🏠** | **Street — FINAL** | 12 | #12 |

---

*hello_world · GitHub Workflow Tutorial · Linux · Windows · macOS · GitHub Actions*  
*From the infinite to Foxtrail Lane, Northport, Alabama — one PR at a time.*
