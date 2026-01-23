# GitHub Setup Guide

Step-by-step instructions for uploading this repository to GitHub.

## Prerequisites

- Git installed on your computer
- GitHub account created
- Basic command line knowledge

## Step 1: Install Git (if not already installed)

### Windows:
1. Download from [git-scm.com](https://git-scm.com/)
2. Run installer with default options
3. Open Git Bash

### macOS:
```bash
# Install using Homebrew
brew install git

# Or install Xcode Command Line Tools
xcode-select --install
```

### Linux:
```bash
# Ubuntu/Debian
sudo apt-get install git

# Fedora
sudo dnf install git
```

## Step 2: Configure Git (First Time Only)

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

## Step 3: Create GitHub Repository

1. Go to [github.com](https://github.com) and login
2. Click the "+" icon in top right
3. Select "New repository"
4. Repository settings:
   - **Name:** `networking-lab` (or your preferred name)
   - **Description:** "Hands-on networking lab documentation for Cisco routing and switching"
   - **Visibility:** Public (to share with others) or Private
   - **DO NOT** initialize with README (we already have one)
5. Click "Create repository"

## Step 4: Prepare Your Local Repository

Navigate to your networking-lab folder:

```bash
cd /path/to/networking-lab
```

Initialize git repository:

```bash
git init
```

## Step 5: Add Files to Git

```bash
# Add all files
git add .

# Check what will be committed
git status

# Commit with message
git commit -m "Initial commit: Complete networking lab documentation"
```

## Step 6: Connect to GitHub

Replace `YOUR_USERNAME` with your GitHub username:

```bash
git remote add origin https://github.com/YOUR_USERNAME/networking-lab.git

# Verify remote was added
git remote -v
```

## Step 7: Push to GitHub

```bash
# Push to GitHub
git branch -M main
git push -u origin main
```

### If prompted for credentials:
- **Username:** Your GitHub username
- **Password:** Your Personal Access Token (not your GitHub password!)

### Creating a Personal Access Token:
1. Go to GitHub Settings
2. Developer settings → Personal access tokens → Tokens (classic)
3. Generate new token
4. Select scopes: `repo` (full control)
5. Copy token (you won't see it again!)
6. Use token as password when pushing

## Step 8: Verify Upload

1. Go to your GitHub repository URL
2. Refresh the page
3. You should see all your files!

## Adding Files Later

When you add screenshots, configs, or Packet Tracer files:

```bash
# Navigate to repository
cd /path/to/networking-lab

# Add new files
git add .

# Or add specific files
git add screenshots/new-screenshot.png

# Commit with descriptive message
git commit -m "Add Project 1 verification screenshots"

# Push to GitHub
git push
```

## Organizing Your Files

### Add Screenshots:
```bash
# Copy screenshot to repository
cp ~/Downloads/screenshot.png networking-lab/screenshots/project1-verification.png

# Add to git
git add screenshots/project1-verification.png
git commit -m "Add Project 1 VLAN verification screenshot"
git push
```

### Add Packet Tracer Files:
```bash
# Copy .pkt file
cp ~/Documents/project1.pkt networking-lab/packet-tracer-files/project1-vlans.pkt

# Add to git
git add packet-tracer-files/project1-vlans.pkt
git commit -m "Add Project 1 Packet Tracer file"
git push
```

### Add Configuration Backups:
```bash
# Copy router config
# (After running: show running-config > router-config.txt)
cp router-config.txt networking-lab/configs/

git add configs/router-config.txt
git commit -m "Add router configuration backup"
git push
```

## Updating README

When you complete more projects:

```bash
# Edit README.md
nano README.md  # or use your preferred editor

# Update project status
# Change: ### Project 2: DHCP Per VLAN
# To:     ### Project 2: DHCP Per VLAN ✅

# Commit changes
git add README.md
git commit -m "Update README: Mark Project 2 as complete"
git push
```

## Common Git Commands

```bash
# Check status
git status

# View commit history
git log

# View changes
git diff

# Undo uncommitted changes
git checkout -- filename

# Remove file from staging
git reset filename

# Update from GitHub (if editing online)
git pull

# Clone repository to another computer
git clone https://github.com/YOUR_USERNAME/networking-lab.git
```

## Making Your Repository Look Professional

### Add Topics (Tags):
1. Go to your GitHub repository
2. Click the gear icon next to "About"
3. Add topics: `cisco`, `networking`, `ccna`, `packet-tracer`, `vlan`, `routing`, `switching`

### Add a License:
```bash
# Add LICENSE file
# Choose from: MIT, Apache 2.0, GPL, etc.
# GitHub provides templates when creating repository
```

### Enable GitHub Pages (Optional):
If you want a website for your documentation:
1. Go to repository Settings
2. Pages → Source → main branch
3. Your docs will be at: `https://YOUR_USERNAME.github.io/networking-lab/`

## Protecting Your Privacy

### DO NOT commit:
- ❌ Real passwords
- ❌ Real IP addresses (if using public IPs)
- ❌ Personal information
- ❌ Company-specific configs

### The .gitignore file handles:
- ✅ Temporary files
- ✅ Backup files
- ✅ Personal notes
- ✅ System files

## Sharing Your Repository

### Get the URL:
```
https://github.com/YOUR_USERNAME/networking-lab
```

### Share on:
- LinkedIn: Add to your projects section
- Resume: Include GitHub link
- Job applications: Showcase your work
- Reddit: Share in r/ccna or r/networking
- Discord: Networking study servers

## Making Your README Stand Out

### Add Badges:
```markdown
![Cisco](https://img.shields.io/badge/Cisco-IOS-blue)
![Status](https://img.shields.io/badge/Status-In%20Progress-yellow)
![License](https://img.shields.io/badge/License-MIT-green)
```

### Add Statistics:
- Lines of code
- Number of commits
- Last updated date

### Add Visuals:
- Screenshots of your topology
- Diagram images
- Success verification screenshots

## Troubleshooting

### Error: "Repository not found"
- Check repository name spelling
- Verify you have access to the repository
- Check your GitHub username

### Error: "Permission denied"
- Make sure you're using correct credentials
- Use Personal Access Token, not password
- Check if you have write access

### Error: "Merge conflict"
```bash
# If you edited files both locally and on GitHub
git pull
# Resolve conflicts manually
git add .
git commit -m "Resolve merge conflicts"
git push
```

### Large File Warning:
GitHub has file size limits (100MB per file). For large Packet Tracer files:
- Use Git LFS (Large File Storage)
- Or link to external storage (Google Drive, Dropbox)

## Next Steps

After uploading:

1. ✅ Add a screenshot to your README
2. ✅ Star your own repository (bookmark)
3. ✅ Share on LinkedIn
4. ✅ Continue documenting your progress
5. ✅ Keep committing as you complete projects

## Example Commit Messages

Good commit messages:
- ✅ "Add Project 1 complete configuration"
- ✅ "Update README with Project 2 completion"
- ✅ "Fix typo in command reference"
- ✅ "Add troubleshooting section for DHCP issues"

Poor commit messages:
- ❌ "Update"
- ❌ "Fix"
- ❌ "Changes"
- ❌ "asdf"

---

## Quick Reference Card

```bash
# One-time setup
git config --global user.name "Your Name"
git config --global user.email "your@email.com"

# Start new repository
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/USERNAME/REPO.git
git push -u origin main

# Daily workflow
git add .
git commit -m "Descriptive message"
git push

# Update from GitHub
git pull
```

---

**You're ready to share your networking journey! 🚀**

*Having issues? Check GitHub's documentation: https://docs.github.com/en/get-started*
