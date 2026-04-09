# Git — GitHub First-Time Setup Cheat Sheet
> Linux · install · configure · authenticate · verify

**Flow:** install git → global config → GitHub account → authenticate (PAT or SSH) → credential store → verify

> **Legend:**
> - `[local]` — commands run in your terminal
> - `[web UI]` — actions taken in GitHub
> - `[run once]` — only needed the first time, globally

---

## 1. Install Git `[local]`

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

## 2. Global Identity & Config `[run once]`

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

## 3. Create / Configure GitHub Account `[web UI]`

| Action | Why |
|---|---|
| Sign up or log in at github.com | Use the same email as `user.email` above |
| Settings → Emails → verify your email address | GitHub won't accept pushes from an unverified email |
| Settings → Profile → add name, avatar (optional) | Helps collaborators identify you in PRs and reviews |
| Settings → Security → enable two-factor authentication (2FA) | Required for most organisations; strongly recommended for all |

> **Warning:** GitHub no longer accepts plain passwords for Git operations. You must authenticate
> using a Personal Access Token (HTTPS) or an SSH key.

---

## 4. Choose an Authentication Method

| Method | Best for |
|---|---|
| **Personal Access Token (PAT)** — HTTPS | Simpler setup; token acts as your password; works anywhere; best for getting started |
| **SSH Key** — SSH | More secure; no token to expire; password-free after setup; preferred for daily dev work |

> **Note:** You only need one method. Use PAT for simplicity; SSH for long-term use. Both are covered below.

---

## 5. Option A — Personal Access Token (PAT) `[HTTPS]`

### Generate the token `[web UI]`

| Action | Detail |
|---|---|
| GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic) → Generate new token | Set expiry; tick scopes: `repo`, `workflow` |
| Copy the token immediately | It is only shown once — store it in a password manager |

> **Tip:** Use **Fine-grained tokens** for tighter per-repo scope. Classic tokens are simpler for general use.

### Store the token so Git remembers it `[local]`

| Command | Description |
|---|---|
| `git config --global credential.helper store` | Save to `~/.git-credentials` (plaintext — trusted machines only) |
| `git config --global credential.helper 'cache --timeout=3600'` | Cache in memory for 1 hour (more secure) |
| `git config --global credential.helper /usr/share/doc/git/contrib/credential/libsecret/git-credential-libsecret` | Use GNOME Keyring / libsecret (most secure) |

> **Note:** After setting a credential helper, the first `git push` will prompt for username + token.
> Git stores it automatically after that.

---

## 6. Option B — SSH Key `[SSH]`

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

## 7. Verify the Connection `[local]`

| Command | Description |
|---|---|
| `ssh -T git@github.com` | Test SSH auth — expect: `Hi <username>! You've successfully authenticated` |
| `git config --global --list` | Confirm all global settings are correct |
| `git clone git@github.com:<user>/<repo>.git` | SSH clone — confirms end-to-end auth works |
| `git clone https://github.com/<user>/<repo>.git` | HTTPS clone — confirms PAT auth works |

> **Tip:** Run `ssh -vT git@github.com` for verbose output if the connection test fails —
> it will show exactly where authentication breaks down.

---

## 8. Full Setup Sequence (Quick Reference)

```bash
# --- install ---
sudo apt install -y git && git --version

# --- global config ---
git config --global user.name "Your Name"
git config --global user.email "you@email.com"
git config --global init.defaultBranch main
git config --global pull.rebase true

# --- SSH key (recommended) ---
ssh-keygen -t ed25519 -C "you@email.com"
eval "$(ssh-agent -s)" && ssh-add ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub     # paste into GitHub → Settings → SSH keys

# --- verify ---
ssh -T git@github.com
```
