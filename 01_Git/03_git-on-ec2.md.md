# Git on AWS EC2

## 1. Overview

Git can be installed on an AWS EC2 Linux instance to manage source code and synchronize changes with GitHub.

**What we will configure:**

- Git installation and global configuration.
- Local repository and GitHub remote.
- GitHub authentication using Fine-grained PAT.
- Credential storage to avoid repeated login.
- Push and pull operations.

## 2. Prerequisites

- Running AWS EC2 Linux instance.
- SSH access to EC2.
- GitHub account.
- Existing GitHub repository.
- Fine-grained Personal Access Token (PAT).

### GitHub PAT Permissions

Generate a Fine-grained token:

GitHub → Settings → Developer settings → Personal access tokens → Fine-grained tokens.

Configure:

| Setting           | Value                      |
| ----------------- | -------------------------- |
| Token name        | EC2-Git-Access             |
| Repository access | Only selected repositories |
| Repository        | `gitpractice`            |
| Contents          | Read and write             |
| Metadata          | Read-only (automatic)      |

Generate and securely save the token.

**Important:** Never expose your PAT in screenshots, chat, Git repositories or shell commands.

---

## 3. Install Git on EC2

Check whether Git is installed:

```bash
git --version
```

Amazon Linux 2023:

```bash
sudo dnf update -y
sudo dnf install git -y
```

Amazon Linux 2:

```bash
sudo yum update -y
sudo yum install git -y
```

Verify:

```bash
git --version
```

---

## 4. Configure Git Identity

Configure the global username and email:

```bash
git config --global user.name "GitPractice"
git config --global user.email "your-email@gmail.com"
```

Verify:

```bash
git config --global --list
```

**Note:** Git identity identifies the commit author. It does not authenticate you with GitHub.

---

## 5. Create and Initialize a Repository

Create a project directory:

```bash
mkdir gitpractice
cd gitpractice
```

Initialize Git:

```bash
git init
```

Create sample files:

```bash
touch linux abc info.txt java
```

Check status:

```bash
git status
```

Stage and commit:

```bash
git add .
git commit -m "Initial commit from EC2"
```

---

## 6. Connect EC2 Repository to GitHub

Create a repository on GitHub, for example:

`https://github.com/USERNAME/gitpractice.git`

Add the remote:

```bash
git remote add origin https://github.com/USERNAME/gitpractice.git
```

Verify:

```bash
git remote -v
```

If a remote already exists, update it:

```bash
git remote set-url origin https://github.com/USERNAME/gitpractice.git
```

**Important:** Use HTTPS authentication for PAT-based Git operations. A PAT cannot authenticate an SSH remote such as `git@github.com:USERNAME/gitpractice.git`.

---

## 7. GitHub Authentication Using Fine-grained PAT

GitHub does not support account passwords for Git operations over HTTPS. Use your PAT instead.

Push:

```bash
git push -u origin main
```

Git prompts for credentials:

```text
Username for 'https://github.com':
```

Enter your GitHub username.

```text
Password for 'https://USERNAME@github.com':
```

Paste your Fine-grained PAT.

The terminal will not display the pasted token. This is normal.

**Authentication requirements:**

- Correct GitHub username.
- Valid, unexpired PAT.
- Repository selected in token permissions.
- Contents permission set to Read and write.
- Organization approval, if applicable.

---

## 8. Save PAT to Avoid Repeated Login

Git provides a credential helper called `store` to save HTTPS credentials locally.

### Step 1: Enable credential storage

```bash
git config --global credential.helper store
```

Verify:

```bash
git config --global --get credential.helper
```

Expected:

```text
store
```

### Step 2: Authenticate once

```bash
git push -u origin main
```

Enter your GitHub username and PAT when prompted.

After successful authentication, Git stores the credentials locally.

### Step 3: Credential storage location

The default location is:

```text
~/.git-credentials
```

For the EC2 `ec2-user` account:

```text
/home/ec2-user/.git-credentials
```

### Step 4: Verify saved credentials

Check whether the file exists:

```bash
ls -l /home/ec2-user/.git-credentials
```

Check permissions:

```bash
stat /home/ec2-user/.git-credentials
```

To inspect the actual contents:

```bash
cat /home/ec2-user/.git-credentials
```

<escape> This displays the actual PAT in plaintext. Never share or screenshot the output.</escape>

### Step 5: Secure the credential file

```bash
chmod 600 /home/ec2-user/.git-credentials
```

Expected permissions:

```text
-rw------- 
```

Only the file owner can read and write it.

### Step 6: Test

```bash
git push
```

Git should now use the saved credentials without requesting your username and PAT again.

### Credential storage flow

```text
git push
    |
    v
Git Credential Helper
    |
    v
~/.git-credentials
    |
    v
Retrieve Saved PAT
    |
    v
GitHub Authentication
    |
    v
Push Successful
```

**Security:** The `store` helper saves credentials in plaintext. File permissions restrict ordinary user access but do not encrypt the token or protect it from root access. Use a secure credential manager or SSH authentication for production environments.

---

## 9. Daily Git Operations

After initial authentication and upstream configuration, these are the usual commands.

| Operation           | Command                     |
| ------------------- | --------------------------- |
| Check changes       | `git status`              |
| Stage all changes   | `git add .`               |
| Commit changes      | `git commit -m "message"` |
| Push changes        | `git push`                |
| Pull changes        | `git pull`                |
| View commit history | `git log --oneline`       |
| View differences    | `git diff`                |
| View remote URL     | `git remote -v`           |
| List branches       | `git branch`              |

### Typical workflow

```bash
git status
git add .
git commit -m "Updated files"
git push
```

Download remote changes:

```bash
git pull
```

---

## 10. Credential Management

| Task                      | Command                                           |
| ------------------------- | ------------------------------------------------- |
| Check credential helper   | `git config --global --get credential.helper`   |
| Check credential file     | `ls -l ~/.git-credentials`                      |
| Check file location       | `echo "$HOME/.git-credentials"`                 |
| Secure credential file    | `chmod 600 ~/.git-credentials`                  |
| Remove saved credentials  | `rm -f ~/.git-credentials`                      |
| Disable credential helper | `git config --global --unset credential.helper` |

If your PAT expires or is revoked:

1. Generate a replacement PAT.
2. Remove the old saved credentials.
3. Authenticate again.

```bash
rm -f ~/.git-credentials
git push
```

**Linux user consideration:**

Git credentials are stored separately for each Linux user.

| User     | Credential location                 |
| -------- | ----------------------------------- |
| ec2-user | `/home/ec2-user/.git-credentials` |
| ubuntu   | `/home/ubuntu/.git-credentials`   |
| root     | `/root/.git-credentials`          |

Avoid using `sudo` for normal Git operations because it may use root's Git configuration and credentials.

---

## 11. Common Errors and Solutions

### Permission denied (publickey)

```text
git@github.com: Permission denied (publickey).
```

**Reason:** SSH remote is being used without a configured SSH key.

**Solution:** Switch to HTTPS.

```bash
git remote set-url origin https://github.com/USERNAME/gitpractice.git
```

### Invalid username or token

```text
remote: Invalid username or token.
fatal: Authentication failed.
```

**Possible causes:**

- Incorrect or expired PAT.
- Missing repository access.
- Missing Contents: Read and write permission.
- Organization approval required.

**Solution:** Verify token permissions and authenticate with a valid PAT.

### Password authentication is not supported

**Reason:** GitHub account password was entered instead of a PAT.

**Solution:** Enter your PAT at the HTTPS password prompt.

### Repository not found

**Reason:** Incorrect URL or insufficient repository access.

**Solution:**

```bash
git remote -v
```

Verify the repository URL and permissions.

### Updates were rejected

```text
! [rejected] main -> main (fetch first)
```

**Solution:**

```bash
git pull --rebase origin main
git push
```

Resolve any conflicts before pushing.

### Git asks for credentials every time

Check:

```bash
git config --global --get credential.helper
whoami
echo "$HOME"
ls -l ~/.git-credentials
git remote -v
```

Ensure the helper is configured, authentication has succeeded, and Git is running under the correct Linux user.

---

## 12. Important Git Concepts for Interviews

| Concept              | Explanation                                    |
| -------------------- | ---------------------------------------------- |
| Git                  | Distributed version control system             |
| GitHub               | Cloud platform for hosting Git repositories    |
| Git identity         | Commit author name and email                   |
| PAT                  | Token used for HTTPS authentication            |
| Fine-grained PAT     | Repository-specific token permissions          |
| SSH authentication   | Uses public/private key pairs                  |
| HTTPS authentication | Uses username and PAT                          |
| Credential helper    | Manages Git authentication credentials         |
| Git remote           | Connection between local and remote repository |
| Git commit           | Snapshot of staged changes                     |

### Important Interview Questions

**1. What is the difference between Git identity and authentication?**

Git identity identifies the commit author, whereas authentication verifies access to GitHub.

**2. Where are credentials stored using Git's store helper?**

In `~/.git-credentials`. For `ec2-user`, this is `/home/ec2-user/.git-credentials`.

**3. Are credentials encrypted using the store helper?**

No. They are stored in plaintext.

**4. What happens when Git commands are executed using sudo?**

Git may use root's home directory, configuration and credentials.

**5. What is the difference between SSH and HTTPS authentication?**

SSH uses key pairs; HTTPS uses a username and token.

---

## 13. Quick Setup Reference

Use this sequence when configuring a new EC2 instance. Replace the repository details with your own.

```bash
# 1. Install Git (Amazon Linux 2023)
sudo dnf install git -y

# 2. Configure Git identity
git config --global user.name "GitPractice"
git config --global user.email "your-email@gmail.com"

# 3. Create repository
mkdir gitpractice
cd gitpractice
git init

# 4. Create and commit files
touch linux abc info.txt java
git add .
git commit -m "Initial commit from EC2"

# 5. Connect GitHub repository using HTTPS
git remote add origin https://github.com/USERNAME/gitpractice.git

# 6. Configure credential storage
git config --global credential.helper store

# 7. Authenticate and push
git push -u origin main

# Enter GitHub username and PAT when prompted.

# 8. Secure saved credentials
chmod 600 ~/.git-credentials

# 9. Verify credential file exists
ls -l ~/.git-credentials

# 10. Future operations
git add .
git commit -m "Updated files"
git push
git pull
```

---

## 14. Best Practices

1. Use Fine-grained PATs with minimum required permissions.
2. Never expose PATs in code, terminal screenshots or Git repositories.
3. Set an appropriate token expiration.
4. Never commit passwords, API keys or `.env` files.
5. Avoid plaintext credential storage on production servers.
6. Avoid unnecessary use of `sudo` with Git.
7. Use SSH keys or secure credential management for production automation.
8. Revoke unused or compromised tokens.

## Conclusion

Git on EC2 allows developers to manage source code directly from a cloud Linux server and synchronize it with GitHub.

Using HTTPS with a Fine-grained PAT provides authentication, while Git's credential helper can save credentials to avoid repeated login.

For the `ec2-user` account, the default saved credential location is:

```text
/home/ec2-user/.git-credentials
```
