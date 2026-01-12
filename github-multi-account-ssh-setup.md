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
# Personal GitHub
Host github-personal
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_personal
  IdentitiesOnly yes

# Work GitHub
Host github-work
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_work
  IdentitiesOnly yes
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

```bash
git config user.name "Your Name"
git config user.email "your-email@example.com"
```

---

## 10. Mental Model

| Item | Controlled By |
|----|----|
| GitHub account | SSH key |
| Repo context | Folder |
| Account switching | SSH Host alias |
| Commit author | `user.email` |

---

## ✅ Result

Clean, secure, multi-account GitHub SSH setup.
