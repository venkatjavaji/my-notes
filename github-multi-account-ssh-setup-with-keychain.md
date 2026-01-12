# GitHub Multi-Account SSH Setup (Mac)

This document explains how to set up **multiple GitHub accounts (personal + work)** on a single Mac using **SSH with ed25519 keys**.

This setup:
- Prevents wrong-account pushes
- Avoids password / token confusion
- Scales cleanly for multiple repos and orgs
- Is the recommended professional approach

---

## 1. Prerequisites

- macOS
- Git installed (`git --version`)
- GitHub accounts (personal + work)

---

## 2. Clean State Assumptions

- No global Git config (`~/.gitconfig` may not exist)
- No existing SSH keys (or intentionally ignored)

Check:
```bash
git config --global --list
ls ~/.ssh
```

---

## 3. Create SSH Keys (ed25519)

### 3.1 Create `.ssh` directory
```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```

### 3.2 Create **work** SSH key
```bash
ssh-keygen -t ed25519 -C "work-email@company.com"
```
When prompted:
```
~/.ssh/id_ed25519_work
```

### 3.3 Create **personal** SSH key
```bash
ssh-keygen -t ed25519 -C "personal-email@gmail.com"
```
When prompted:
```
~/.ssh/id_ed25519_personal
```

### 3.4 Verify keys
```bash
ls -l ~/.ssh
```

Expected:
```
id_ed25519_work
id_ed25519_work.pub
id_ed25519_personal
id_ed25519_personal.pub
```

---

## 4. Start SSH Agent & Load Keys

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519_work
ssh-add ~/.ssh/id_ed25519_personal
```

Verify:
```bash
ssh-add -l
```

---

## 5. Configure SSH Routing

Edit:
```bash
nano ~/.ssh/config
```

Add:
```ssh
# =========================
# Personal GitHub account
# =========================
Host github-personal
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_personal
  IdentitiesOnly yes
  AddKeysToAgent yes
  UseKeychain yes

# =========================
# Work GitHub account
# =========================
Host github-work
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_work
  IdentitiesOnly yes
  AddKeysToAgent yes
  UseKeychain yes
```

---

## 6. Add SSH Keys to GitHub

### Work account
```bash
cat ~/.ssh/id_ed25519_work.pub
```

### Personal account
```bash
cat ~/.ssh/id_ed25519_personal.pub
```

Add each key in:
GitHub → Settings → SSH & GPG keys → New SSH key

---

## 7. Test Authentication

```bash
ssh -T git@github-work
ssh -T git@github-personal
```

Expected:
```
Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.
```

---

## 8. Clone Repositories

### Work repo
```bash
git clone git@github-work:org-or-user/repo.git
```

### Personal repo
```bash
git clone git@github-personal:username/repo.git
```

---

## 9. Set Commit Identity (Per Repo)

Inside each repository (one-time):

### Work repo
```bash
git config user.name "Your Name"
git config user.email "work-email@company.com"
```

### Personal repo
```bash
git config user.name "Your Name"
git config user.email "personal-email@gmail.com"
```

Verify:
```bash
git config --local --list
```

---

## 10. Disable SSH Passphrase Prompt (macOS Keychain)

If you created SSH keys **with a passphrase**, Git may prompt for it on every `git pull` or `git push`.

Follow these steps to **enter the passphrase once and never be prompted again**.

### 10.1 Ensure SSH config supports Keychain

Your `~/.ssh/config` **must include**:
```ssh
AddKeysToAgent yes
UseKeychain yes
```

(This is already included in Section 5.)

---

### 10.2 Add keys to macOS Keychain (one-time)

```bash
ssh-add --apple-use-keychain ~/.ssh/id_ed25519_work
ssh-add --apple-use-keychain ~/.ssh/id_ed25519_personal
```

- Enter the passphrase **once**
- macOS Keychain securely stores it
- No future prompts on `git pull`, `git push`, etc.

---

### 10.3 Verify keys are cached

```bash
ssh-add -l
```

Both keys should be listed.

---

## 11. Mental Model

| Item | Controlled By |
|----|----|
| GitHub account | SSH key |
| Repo context | Folder |
| Account switching | SSH Host alias |
| Commit author | `user.email` |
| Passphrase prompt | SSH agent / Keychain |

---

## ✅ Final Result

- Clean multi-account GitHub setup
- Secure SSH authentication
- No repeated passphrase prompts
- Professional, scalable workflow
