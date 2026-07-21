# GitHub SSH Setup and First Push Guide (Kali Linux)

# Prerequisites

You need:

- A GitHub account
- Your GitHub username
- Your GitHub email address
- Kali Linux with an Internet connection

Throughout this guide replace:

- `YOUR_GITHUB_USERNAME`
- `YOUR_EMAIL@example.com`
- `YOUR_REPOSITORY_NAME`

with your own values.

---

# Step 1. Install Git

```bash
sudo apt update
sudo apt install git -y
```

**Explanation**

Installs Git, which is used to communicate with GitHub.

Verify:

```bash
git --version
```

---

# Step 2. Generate an SSH Key

Run:

```bash
ssh-keygen -t ed25519 -C "YOUR_EMAIL@example.com"
```

**Explanation**

- `-t ed25519` creates a modern and secure SSH key.
- `-C` adds your email as a label.

When asked:

```
Enter file in which to save the key
```

Press **Enter**.

Default location:

```
~/.ssh/id_ed25519
```

When asked for a passphrase:

- Press Enter for no passphrase
- Or enter one for extra security

---

# Step 3. Start the SSH Agent

```bash
eval "$(ssh-agent -s)"
```

**Explanation**

Starts the background service that stores your SSH keys.

---

# Step 4. Add the SSH Key

```bash
ssh-add ~/.ssh/id_ed25519
```

**Explanation**

Loads your SSH key into the SSH agent.

---

# Step 5. Display Your Public Key

```bash
cat ~/.ssh/id_ed25519.pub
```

Copy the entire output.

It will look similar to:

```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAI..... YOUR_EMAIL@example.com
```

---

# Step 6. Add the Key to GitHub

Open your browser.

Go to:

GitHub → Profile Picture → Settings → SSH and GPG Keys

Click:

**New SSH Key**

Fill in:

- Title: Kali Laptop
- Key Type: Authentication Key
- Key: Paste the copied public key

Click **Add SSH Key**.

---

# Step 7. Test the Connection

```bash
ssh -T git@github.com
```

If asked:

```
Are you sure you want to continue connecting?
```

Type:

```
yes
```

Expected result:

```
Hi YOUR_GITHUB_USERNAME!
You've successfully authenticated...
```

---

# Step 8. Configure Git Identity

```bash
git config --global user.name "YOUR_GITHUB_USERNAME"

git config --global user.email "YOUR_EMAIL@example.com"
```

Verify:

```bash
git config --global --list
```

---

# Step 9. Create a Repository Using GitHub Website

1. Login to GitHub.
2. Click **New Repository**.
3. Repository Name:

```
YOUR_REPOSITORY_NAME
```

4. Choose Public or Private.
5. Do NOT initialize with README.
6. Click **Create Repository**.

---

# Step 10. Copy the SSH URL

After the repository is created click the **SSH** button.

Example:

```
git@github.com:YOUR_GITHUB_USERNAME/YOUR_REPOSITORY_NAME.git
```

Copy this URL.

---

# Step 11. Create a Local Project

```bash
mkdir YOUR_REPOSITORY_NAME
cd YOUR_REPOSITORY_NAME
```

**Explanation**

Creates your project folder.

---

# Step 12. Initialize Git

```bash
git init
```

**Explanation**

Creates a local Git repository.

---

# Step 13. Connect to GitHub

```bash
git remote add origin git@github.com:YOUR_GITHUB_USERNAME/YOUR_REPOSITORY_NAME.git
```

Verify:

```bash
git remote -v
```

Expected:

```
origin git@github.com:YOUR_GITHUB_USERNAME/YOUR_REPOSITORY_NAME.git
```

---

# Step 14. Create Your First File

```bash
echo "# My First GitHub Repository" > README.md
```

Check:

```bash
ls
```

You should see:

```
README.md
```

---

# Step 15. Stage the File

```bash
git add .
```

**Explanation**

Stages all files for commit.

Check status:

```bash
git status
```

---

# Step 16. Create Your First Commit

```bash
git commit -m "Initial commit"
```

**Explanation**

Saves a snapshot of your project.

---

# Step 17. Rename the Branch

```bash
git branch -M main
```

Most GitHub repositories use the `main` branch.

---

# Step 18. Push to GitHub

```bash
git push -u origin main
```

**Explanation**

Uploads your project to GitHub.

`-u` remembers the remote so future pushes only require:

```bash
git push
```

---

# Step 19. Verify

Refresh your GitHub repository page.

You should now see:

- README.md
- Commit history

Congratulations! Your first project has been successfully pushed to GitHub.

---

# Useful Commands

Show repository status

```bash
git status
```

Show commit history

```bash
git log
```

Check remote repository

```bash
git remote -v
```

Pull latest changes

```bash
git pull
```

Push new changes

```bash
git push
```

Clone a repository

```bash
git clone git@github.com:YOUR_GITHUB_USERNAME/YOUR_REPOSITORY_NAME.git
