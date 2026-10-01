# GitHub SSH Setup and First Push
## 1. Configure Git Identity
```bash
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
```
## 2. Create and Add SSH Key
```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub
```
Copy the public key, then in GitHub open **Settings > SSH and GPG keys > New SSH key**, paste it, and save.
## 3. Test GitHub SSH Authentication
```bash
ssh -T git@github.com
```
Accept the GitHub host fingerprint if prompted during the first connection.
## 4. Connect a Local Project and Push
```bash
cd /path/to/your/project
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin git@github.com:USERNAME/REPOSITORY.git
git remote -v
git push -u origin main
```
