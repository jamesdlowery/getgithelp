# hello_world

A complete GitHub workflow tutorial project — 12 incremental releases of a Python "Hello World" script, automatically built as native executables for Linux, Windows, and macOS via GitHub Actions.

---

## What This Repository Is

This repository is the hands-on companion to a GitHub workflow tutorial. It demonstrates professional software development practices using a deliberately simple program — a Python script that prints greetings — so that the workflow itself is the focus, not the code.

Starting with "Hello, God!" and zooming in across 12 releases to "Hello, Foxtrail!" (a street in Northport, Alabama), each release practices the full cycle: GitHub Issue → code → automated builds → pull request → promotion → published GitHub Release.

---

## Branch Structure

```
prod  <-- authoritative, production-ready code
  ^
test  <-- acceptance testing stage
  ^
dev   <-- active development
```

All work begins in `dev`. Changes promote to `test` via pull request, then from `test` to `prod` via a second pull request. Each branch has its own build trigger — no branch can be built using another branch's tag.

---

## Automated Builds — GitHub Actions

Three workflows run in `.github/workflows/`. Each is triggered by a branch-scoped tag and builds native executables for Linux, Windows, and macOS in parallel using PyInstaller.

| Tag pushed | Workflow | Output | Stored as |
|------------|----------|--------|-----------|
| `dev-vX.X` | `build-dev.yml` | `hello_world_dev_vX.X_linux` · `_windows.exe` · `_macos` | Actions Artifacts |
| `test-vX.X` | `build-test.yml` | `hello_world_test_vX.X_linux` · `_windows.exe` · `_macos` | Draft GitHub Release |
| `prod-vX.X` | `build-prod.yml` | `hello_world_prod_vX.X_linux` · `_windows.exe` · `_macos` | Published GitHub Release |

No cross-contamination: `dev-v*` tags only build dev executables, `test-v*` tags only build test executables, and so on.

---

## Source File Naming Convention

Source files are renamed at each promotion stage using `git mv`:

| Stage | File name |
|-------|-----------|
| dev   | `hello_world_dev_vX.X.py` |
| test  | `hello_world_test_vX.X.py` |
| prod  | `hello_world_prod_vX.X.py` |

The filename in the branch must match what the build workflow expects. The workflow extracts the version from the tag (e.g., `dev-v1.1` strips to `v1.1`) and builds from `hello_world_dev_v1.1.py`.

---

## Release History

| prod Tag | Greeting | Scope | Lines | Issue |
|----------|----------|-------|-------|-------|
| `prod-v0.1` | Hello, God! | The Divine / Infinite | 1 | #1 |
| `prod-v0.2` | Hello, Universe! | All of existence | 2 | #2 |
| `prod-v0.3` | Hello, Local Group! | Our galaxy cluster | 3 | #3 |
| `prod-v0.4` | Hello, Milky Way! | Our galaxy | 4 | #4 |
| `prod-v0.5` | Hello, Solar System! | Our star system | 5 | #5 |
| **`prod-v1.0`** | **Hello, World!** | **Our planet — MAJOR** | 6 | #6 |
| `prod-v1.1` | Hello, United States! | Country | 7 | #7 |
| `prod-v1.2` | Hello, Alabama! | State | 8 | #8 |
| `prod-v1.3` | Hello, Tuscaloosa County! | County | 9 | #9 |
| `prod-v1.4` | Hello, Northport! | City | 10 | #10 |
| `prod-v1.5` | Hello, Buckhead! | Neighborhood | 11 | #11 |
| **`prod-v1.6`** | **Hello, Foxtrail!** | **Street — FINAL** | 12 | #12 |

---

## Final Output (v1.6)

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

---

## Per-Release Workflow (14 steps)

1. Open a GitHub Issue
2. Create versioned source file on `dev`
3. Test the Python script locally
4. Commit and push to `dev`
5. Push tag `dev-vX.X` → triggers `build-dev.yml` → 3 dev executables as Actions Artifacts
6. Download and test dev executables on all 3 platforms
7. Open PR: `dev` → `test` (body: `refs #N`), get reviewed, merge
8. On `test`: rename file (`git mv hello_world_dev_vX.X.py hello_world_test_vX.X.py`), commit, push
9. Push tag `test-vX.X` → triggers `build-test.yml` → 3 test executables on Draft GitHub Release
10. Download and acceptance test all 3 test executables
11. Open PR: `test` → `prod` (body: `closes #N`), get reviewed, merge
12. On `prod`: rename file (`git mv hello_world_test_vX.X.py hello_world_prod_vX.X.py`), commit, push
13. Push tag `prod-vX.X` → triggers `build-prod.yml` → 3 prod executables on Published GitHub Release
14. Sync `test` and `dev` back to `prod`; issue auto-closes

---

## Commit Message Convention

This project uses [Conventional Commits](https://www.conventionalcommits.org/):

```
feat: add Hello Northport (v1.4) refs #10
chore: rename to hello_world_test_v1.4.py for test stage
chore: add build-dev, build-test, build-prod GitHub Actions workflows
```

Types used: `feat`, `fix`, `chore`, `docs`, `ci`, `refactor`

---

## Prerequisites

- **Git** — version control
- **Python 3** — to run `.py` scripts locally
- **PyInstaller** (`pip3 install pyinstaller`) — for local builds; GitHub Actions handles this in CI
- **gh CLI** — for issue creation, PR management, and artifact download
- **GitHub account** — with a Personal Access Token (classic) scoped to `repo`, `workflow`, `read:org`

---

*hello_world · GitHub Workflow Tutorial · Linux · Windows · macOS · GitHub Actions*
*From the infinite to Foxtrail Lane, Northport, Alabama — one PR at a time.*
