# Git & GitHub Complete Guide

## Table of Contents

1. [Understanding Git vs GitHub](#understanding-git-vs-github)
2. [Installation and Setup](#installation-and-setup)
3. [Git Basics](#git-basics)
4. [GitHub Essentials](#github-essentials)
5. [Common Workflows](#common-workflows)
6. [Real-Life Web Dev Scenarios](#real-life-web-dev-scenarios)
7. [Branching Strategies](#branching-strategies)
8. [Collaboration & Pull Requests](#collaboration--pull-requests)
9. [Best Practices](#best-practices)
10. [Troubleshooting](#troubleshooting)

## Understanding Git vs GitHub

### Git
- **Version control system** that tracks changes in your code
- Works locally on your computer
- Created by Linus Torvalds in 2005
- Free and open-source

### GitHub
- **Cloud-based hosting service** for Git repositories
- Provides collaboration features (pull requests, issues, etc.)
- Web interface for Git repositories
- Owned by Microsoft

**Analogy**: Git is like a save system for your code; GitHub is like Google Drive for your Git repositories.

## Installation and Setup

### Installing Git

#### Linux
```bash
# Debian/Ubuntu
sudo apt update
sudo apt install git

# Fedora
sudo dnf install git

# Arch
sudo pacman -S git
```

#### macOS
```bash
# Using Homebrew
brew install git

# Or download from git-scm.com
# Or use Xcode Command Line Tools
xcode-select --install
```

#### Windows/WSL
```bash
# In WSL
sudo apt update
sudo apt install git

# In Windows (download from git-scm.com)
# Or use: winget install Git.Git
```

### Verify Installation
```bash
git --version
```

### Initial Configuration

```bash
# Set your identity (required)
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Set default branch name
git config --global init.defaultBranch main

# Set default editor
git config --global core.editor "code --wait"  # VS Code
# git config --global core.editor "nano"       # Nano
# git config --global core.editor "vim"        # Vim

# Enable color output
git config --global color.ui auto

# View all settings
git config --list

# View specific setting
git config user.name
```

### Separate Git Identities for Work and Personal Projects

If you work on both personal and work projects, you can configure Git to automatically use different identities based on the directory.

#### Method 1: Using Conditional Includes (Recommended)

**Step 1: Create a separate config file for work**

```bash
# Create work-specific Git config
cat > ~/.gitconfig-work << 'EOF'
[user]
    name = Your Work Name
    email = work@company.com
EOF
```

**Step 2: Edit your main ~/.gitconfig**

```bash
# Open your main Git config
nano ~/.gitconfig
# or
code ~/.gitconfig
```

**Add this configuration:**

```ini
# ~/.gitconfig

# 1. DEFAULT (Personal)
# This applies to all repositories by default
[user]
    name = Your Personal Name
    email = personal@gmail.com

[init]
    defaultBranch = main

[color]
    ui = auto

[core]
    editor = code --wait

# 2. WORK PROJECTS
# Override settings for work repositories
[includeIf "gitdir:~/work/"]
    path = ~/.gitconfig-work

# For Windows/WSL with D: drive
[includeIf "gitdir:/mnt/d/work-projects/"]
    path = ~/.gitconfig-work

# You can add multiple work directories
[includeIf "gitdir:~/company-repos/"]
    path = ~/.gitconfig-work
```

**Step 3: Verify configuration**

```bash
# Check global config (shows personal by default)
git config --global user.email
# Output: personal@gmail.com

# Go to a work directory
cd ~/work/some-project

# Check config in work directory
git config user.email
# Output: work@company.com

# Or check all settings
git config --list --show-origin
# Shows where each setting comes from
```

#### Method 2: Multiple Work Accounts

If you have multiple work accounts (e.g., different clients):

```bash
# Create separate config files
cat > ~/.gitconfig-work-clientA << 'EOF'
[user]
    name = Your Name
    email = you@clientA.com
EOF

cat > ~/.gitconfig-work-clientB << 'EOF'
[user]
    name = Your Name
    email = you@clientB.com
EOF

# Edit ~/.gitconfig
cat >> ~/.gitconfig << 'EOF'
[includeIf "gitdir:~/projects/clientA/"]
    path = ~/.gitconfig-work-clientA

[includeIf "gitdir:~/projects/clientB/"]
    path = ~/.gitconfig-work-clientB
EOF
```

#### Important Notes for Conditional Includes

```bash
# 1. Directory paths must end with a slash /
[includeIf "gitdir:~/work/"]         # ✅ Correct
[includeIf "gitdir:~/work"]          # ❌ Won't work

# 2. Use ~/ for home directory
[includeIf "gitdir:~/projects/"]     # ✅ Correct

# 3. For WSL, use /mnt/ paths for Windows drives
[includeIf "gitdir:/mnt/d/work/"]    # ✅ Correct for D: drive
[includeIf "gitdir:/mnt/c/Users/you/work/"]  # ✅ For C: drive

# 4. Use /** to match all subdirectories recursively
[includeIf "gitdir:~/work/**"]       # Matches all repos under ~/work/

# 5. Case sensitivity matters on Linux/macOS
[includeIf "gitdir:~/Work/"]         # Different from ~/work/
```

#### Method 3: Per-Repository Configuration (Manual)

If you don't want automatic switching, set identity per repository:

```bash
# Clone a work repository
git clone git@github.com:company/work-repo.git
cd work-repo

# Set work identity for this repo only (no --global)
git config user.name "Your Work Name"
git config user.email "work@company.com"

# Verify
git config user.email
# Output: work@company.com

# This only affects the current repository
# The setting is saved in .git/config (not ~/.gitconfig)
```

#### Complete Example Setup

```bash
# 1. Create work config file
cat > ~/.gitconfig-work << 'EOF'
[user]
    name = Jane Smith
    email = jane.smith@techcorp.com

[core]
    sshCommand = "ssh -i ~/.ssh/id_ed25519_work"
EOF

# 2. Edit main config
cat > ~/.gitconfig << 'EOF'
# Personal (default)
[user]
    name = Jane Smith
    email = jane.personal@gmail.com

[init]
    defaultBranch = main

[color]
    ui = auto

[core]
    editor = code --wait
    sshCommand = "ssh -i ~/.ssh/id_ed25519"

# Work projects
[includeIf "gitdir:~/work/"]
    path = ~/.gitconfig-work

[includeIf "gitdir:/mnt/d/work-projects/"]
    path = ~/.gitconfig-work
EOF

# 3. Test it
cd ~
git config user.email
# Output: jane.personal@gmail.com

cd ~/work/some-project
git config user.email
# Output: jane.smith@techcorp.com
```

#### Using Different SSH Keys for Work and Personal

You can also configure different SSH keys:

```bash
# 1. Generate separate SSH keys
ssh-keygen -t ed25519 -C "personal@gmail.com" -f ~/.ssh/id_ed25519
ssh-keygen -t ed25519 -C "work@company.com" -f ~/.ssh/id_ed25519_work

# 2. Add to SSH agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
ssh-add ~/.ssh/id_ed25519_work

# 3. Create SSH config
cat > ~/.ssh/config << 'EOF'
# Personal GitHub
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519

# Work GitHub
Host github-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_work
EOF

# 4. Add work SSH key to .gitconfig-work
cat > ~/.gitconfig-work << 'EOF'
[user]
    name = Your Work Name
    email = work@company.com

[core]
    sshCommand = "ssh -i ~/.ssh/id_ed25519_work"
EOF

# 5. Clone work repos with work SSH key
git clone git@github-work:company/repo.git

# Or update existing repo
cd work-repo
git remote set-url origin git@github-work:company/repo.git
```

#### Troubleshooting Multiple Identities

```bash
# Check which config file is being used
git config --list --show-origin | grep user.email
# Shows file path for each setting

# Check effective configuration in current directory
git config user.name
git config user.email

# Show all configurations and their sources
git config --list --show-origin --show-scope

# Verify SSH key being used
ssh -T git@github.com           # Personal
ssh -T git@github-work          # Work

# If wrong identity is used, check:
# 1. Directory path in includeIf has trailing /
# 2. You're actually inside the specified directory
# 3. The path in includeIf matches your actual path
pwd  # Check current directory

# Force reload config (usually automatic)
cd .. && cd -
```

#### Best Practices

```bash
# 1. Keep work and personal projects in separate directories
~/personal/
  ├── project1/
  └── project2/

~/work/
  ├── work-project1/
  └── work-project2/

# 2. Always verify identity before first commit in new repo
git config user.email

# 3. Create an alias to check identity
git config --global alias.whoami "config user.email"
# Usage: git whoami

# 4. Test your setup after configuration
cd ~/personal/test-repo
git config user.email  # Should show personal email

cd ~/work/test-repo
git config user.email  # Should show work email

# 5. Add a pre-commit hook to verify identity (optional)
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash
EMAIL=$(git config user.email)
if [[ "$EMAIL" != *"@company.com" ]]; then
    echo "Warning: Not using work email!"
    echo "Current email: $EMAIL"
    read -p "Continue? (y/n) " -n 1 -r
    echo
    if [[ ! $REPLY =~ ^[Yy]$ ]]; then
        exit 1
    fi
fi
EOF
chmod +x .git/hooks/pre-commit
```

### SSH Key Setup (Recommended)

```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "your.email@example.com"

# Start SSH agent
eval "$(ssh-agent -s)"

# Add key to SSH agent
ssh-add ~/.ssh/id_ed25519

# Copy public key to clipboard
# Linux
cat ~/.ssh/id_ed25519.pub

# macOS
pbcopy < ~/.ssh/id_ed25519.pub

# WSL
cat ~/.ssh/id_ed25519.pub | clip.exe

# Add to GitHub:
# Go to GitHub.com → Settings → SSH and GPG keys → New SSH key
# Paste the key and save

# Test connection
ssh -T git@github.com
```

## Git Basics

### Creating a Repository

```bash
# Create new directory and initialize Git
mkdir my-project
cd my-project
git init

# Or initialize in existing directory
cd existing-project
git init
```

### Basic Git Workflow

```bash
# Check repository status
git status

# Add files to staging area
git add filename.txt          # Add specific file
git add .                     # Add all files in current directory
git add *.js                  # Add all JavaScript files
git add src/                  # Add entire directory

# Commit changes
git commit -m "Add initial files"

# Add and commit in one step (only for tracked files)
git commit -am "Update existing files"

# View commit history
git log
git log --oneline             # Compact view
git log --graph --oneline     # Visual branch graph
git log -n 5                  # Last 5 commits
```

### Checking Changes

```bash
# See unstaged changes
git diff

# See staged changes
git diff --staged

# See changes in specific file
git diff filename.txt

# See changes between commits
git diff commit1 commit2
```

### Undoing Changes

```bash
# Discard changes in working directory
git checkout -- filename.txt

# Unstage file (keep changes)
git reset filename.txt

# Unstage all files
git reset

# Undo last commit (keep changes)
git reset --soft HEAD~1

# Undo last commit (discard changes) - DANGEROUS!
git reset --hard HEAD~1

# Revert a commit (creates new commit)
git revert commit-hash
```

## GitHub Essentials

### Creating a Repository on GitHub

1. Go to GitHub.com → Click "+" → New repository
2. Name your repository
3. Choose public or private
4. Optionally add README, .gitignore, license
5. Click "Create repository"

### Connecting Local to GitHub

```bash
# Connect existing local repo to GitHub
git remote add origin git@github.com:username/repo-name.git

# Verify remote
git remote -v

# Push to GitHub (first time)
git push -u origin main

# Subsequent pushes
git push
```

### Cloning a Repository

```bash
# Clone via SSH (recommended)
git clone git@github.com:username/repo-name.git

# Clone via HTTPS
git clone https://github.com/username/repo-name.git

# Clone into specific directory
git clone git@github.com:username/repo-name.git my-folder

# Clone specific branch
git clone -b branch-name git@github.com:username/repo-name.git
```

### Fetching and Pulling

```bash
# Fetch changes (doesn't merge)
git fetch origin

# Pull changes (fetch + merge)
git pull

# Pull specific branch
git pull origin main

# Pull with rebase
git pull --rebase
```

## Common Workflows

### Starting a New Project

```bash
# Method 1: Start locally, then push to GitHub
mkdir my-app
cd my-app
git init
echo "# My App" > README.md
git add .
git commit -m "Initial commit"

# Create repo on GitHub, then:
git remote add origin git@github.com:username/my-app.git
git push -u origin main

# Method 2: Start on GitHub, then clone
# Create repo on GitHub first, then:
git clone git@github.com:username/my-app.git
cd my-app
# Start coding!
```

### Daily Development Workflow

```bash
# 1. Start your day - get latest changes
git pull

# 2. Create a feature branch
git checkout -b feature/new-login-page

# 3. Make changes, check status often
git status

# 4. Stage and commit changes
git add .
git commit -m "Add login form with validation"

# 5. Push to GitHub
git push origin feature/new-login-page

# 6. Create Pull Request on GitHub
# Go to GitHub → Pull Requests → New Pull Request

# 7. After PR is merged, update main
git checkout main
git pull
git branch -d feature/new-login-page  # Delete local branch
```

### Working with Branches

```bash
# List branches
git branch                    # Local branches
git branch -r                 # Remote branches
git branch -a                 # All branches

# Create branch
git branch feature-name

# Switch to branch
git checkout feature-name

# Create and switch in one command
git checkout -b feature-name

# Switch branches (newer Git)
git switch feature-name
git switch -c feature-name    # Create and switch

# Rename branch
git branch -m old-name new-name

# Delete branch
git branch -d feature-name    # Safe delete
git branch -D feature-name    # Force delete

# Delete remote branch
git push origin --delete feature-name
```

### Merging

```bash
# Merge branch into current branch
git checkout main
git merge feature-branch

# Merge with no fast-forward (keeps branch history)
git merge --no-ff feature-branch

# Abort merge if conflicts
git merge --abort
```

### Rebasing

```bash
# Rebase current branch onto main
git checkout feature-branch
git rebase main

# Interactive rebase (clean up commits)
git rebase -i HEAD~3          # Last 3 commits

# Continue after resolving conflicts
git rebase --continue

# Abort rebase
git rebase --abort
```

## Real-Life Web Dev Scenarios

### Scenario 1: Starting a New Web Project

```bash
# Clone or create repo
git clone git@github.com:company/new-website.git
cd new-website

# Install dependencies
npm install

# Create development branch
git checkout -b dev

# Start coding
# ... make changes ...

# Commit often with meaningful messages
git add .
git commit -m "Setup project structure with React and Tailwind"

git add src/components/
git commit -m "Add Header and Footer components"

# Push to GitHub
git push origin dev
```

### Scenario 2: Bug Fix in Production

```bash
# Create hotfix branch from main
git checkout main
git pull
git checkout -b hotfix/login-error

# Fix the bug
# ... edit files ...

# Test locally
npm test

# Commit the fix
git add .
git commit -m "Fix: Resolve login redirect error

- Add null check for user object
- Update redirect logic
- Fixes #123"

# Push and create PR
git push origin hotfix/login-error

# After PR approved and merged:
git checkout main
git pull
git branch -d hotfix/login-error
```

### Scenario 3: Feature Development

```bash
# Create feature branch
git checkout main
git pull
git checkout -b feature/user-dashboard

# Develop feature (commit regularly)
git add src/pages/Dashboard.jsx
git commit -m "Add Dashboard component skeleton"

git add src/pages/Dashboard.jsx src/styles/dashboard.css
git commit -m "Implement dashboard layout and styling"

git add src/api/userService.js
git commit -m "Add user data fetching service"

# Keep branch updated with main
git checkout main
git pull
git checkout feature/user-dashboard
git rebase main

# Resolve conflicts if any, then:
git rebase --continue

# Push to GitHub
git push origin feature/user-dashboard

# Or force push after rebase
git push origin feature/user-dashboard --force-with-lease
```

### Scenario 4: Collaborating with Team

```bash
# Update your local main
git checkout main
git pull

# Create branch for your task
git checkout -b feature/payment-integration

# Work on your feature
# ... make changes ...
git commit -am "Integrate Stripe payment gateway"

# Meanwhile, teammate updates main
# Update your branch with latest main
git fetch origin
git rebase origin/main

# Resolve any conflicts
# ... fix conflicts ...
git add .
git rebase --continue

# Push your branch
git push origin feature/payment-integration

# Create Pull Request on GitHub for code review
```

### Scenario 5: Reviewing Someone's Code

```bash
# Fetch all branches
git fetch origin

# Check out teammate's branch
git checkout -b review-feature origin/feature/new-api

# Review code, test locally
npm install
npm run dev

# If changes needed, leave comments on GitHub PR
# Or make commits directly (if allowed)
git add .
git commit -m "Fix typo in API endpoint"
git push origin review-feature

# After review, switch back
git checkout main
```

### Scenario 6: Working on Multiple Features

```bash
# Feature 1
git checkout -b feature/navbar
# ... work on navbar ...
git commit -am "WIP: Add navbar component"
git push origin feature/navbar

# Need to switch to urgent bug fix
git stash                     # Save current work

# Fix bug
git checkout main
git pull
git checkout -b fix/urgent-bug
# ... fix bug ...
git commit -am "Fix critical bug"
git push origin fix/urgent-bug

# Return to navbar feature
git checkout feature/navbar
git stash pop                 # Restore saved work
# ... continue working ...
```

### Scenario 7: Undoing a Mistake

```bash
# Scenario A: Committed to wrong branch
git checkout main
# Oops, made changes here instead of feature branch
git add .
git commit -m "New feature"

# Solution: Move commit to new branch
git branch feature/my-feature
git reset --hard HEAD~1       # Remove from main
git checkout feature/my-feature
# Commit is now on correct branch!

# Scenario B: Need to undo pushed commit
git revert HEAD               # Creates new commit that undoes last one
git push origin main

# Scenario C: Pushed sensitive data (API keys)
# 1. Remove from code immediately
git add .
git commit -m "Remove sensitive data"
git push origin main

# 2. Rotate/invalidate the exposed keys!
# 3. Consider using git-filter-branch or BFG Repo-Cleaner
```

### Scenario 8: Deploying to Production

```bash
# Typical deployment workflow
# 1. Merge feature to dev branch
git checkout dev
git merge feature/new-feature
git push origin dev

# 2. Test on staging environment
# ... run tests ...

# 3. Merge dev to main for production
git checkout main
git pull
git merge dev
git tag -a v1.2.0 -m "Release version 1.2.0"
git push origin main --tags

# 4. Deploy (depends on your setup)
# Might trigger CI/CD automatically
# Or manual: npm run build && npm run deploy
```

### Scenario 9: Working with .gitignore

```bash
# Create .gitignore file
cat > .gitignore << EOF
# Dependencies
node_modules/
package-lock.json

# Environment variables
.env
.env.local
.env*.local

# Build output
dist/
build/
.next/
out/

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Logs
*.log
npm-debug.log*

# Testing
coverage/
.nyc_output/
EOF

git add .gitignore
git commit -m "Add .gitignore file"

# If you already committed files that should be ignored
git rm --cached node_modules -r
git commit -m "Remove node_modules from tracking"
```

## Branching Strategies

### Git Flow (Traditional)

```bash
# Main branches
main        # Production code
develop     # Integration branch

# Supporting branches
feature/*   # New features
release/*   # Release preparation
hotfix/*    # Production fixes

# Example workflow
git checkout develop
git checkout -b feature/new-feature
# ... develop ...
git checkout develop
git merge feature/new-feature

# Release
git checkout -b release/1.0.0
# ... final testing, version bumps ...
git checkout main
git merge release/1.0.0
git tag v1.0.0
git checkout develop
git merge release/1.0.0
```

### GitHub Flow (Simpler)

```bash
# Only main branch + feature branches
main        # Always deployable

# Example workflow
git checkout main
git pull
git checkout -b feature-name
# ... develop ...
git push origin feature-name
# Create PR → Review → Merge → Deploy
```

### Trunk-Based Development

```bash
# Everyone works on main or short-lived branches
main        # Single branch

# Short-lived feature branches (< 1 day)
git checkout -b quick-feature
# ... develop ...
git push origin quick-feature
# PR → Quick review → Merge

# Use feature flags for incomplete features
```

## Collaboration & Pull Requests

### Creating a Pull Request

```bash
# 1. Push your branch
git push origin feature/my-feature

# 2. Go to GitHub → Pull Requests → New Pull Request
# 3. Select base branch (main) and compare branch (feature/my-feature)
# 4. Write descriptive title and description
# 5. Request reviewers
# 6. Add labels, link issues
# 7. Create Pull Request
```

### Good PR Description Template

```markdown
## Description
Brief description of what this PR does.

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Changes Made
- Added X component
- Updated Y functionality
- Fixed Z bug

## Testing
- [ ] Unit tests pass
- [ ] Manual testing completed
- [ ] Works on mobile/desktop

## Screenshots (if applicable)
[Add screenshots]

## Related Issues
Closes #123
Related to #456
```

### Reviewing a Pull Request

```bash
# Check out PR locally
git fetch origin
git checkout -b review-pr origin/feature-branch

# Or use GitHub CLI
gh pr checkout 123

# Test changes
npm install
npm run dev
npm test

# Leave review on GitHub:
# - Comment on specific lines
# - Request changes or Approve
# - Submit review
```

### Updating a Pull Request

```bash
# Make additional changes
git add .
git commit -m "Address review comments"
git push origin feature-branch

# PR automatically updates

# Squash commits before merge (optional)
git rebase -i HEAD~3          # Interactive rebase
# Mark commits as 'squash' except first
git push origin feature-branch --force-with-lease
```

## Best Practices

### Commit Messages

```bash
# Good commit messages
git commit -m "Add user authentication feature"
git commit -m "Fix: Resolve null pointer in login function"
git commit -m "Refactor: Simplify database query logic"
git commit -m "Docs: Update API documentation"

# Conventional Commits format
# <type>: <description>
#
# <body>
#
# <footer>

# Types: feat, fix, docs, style, refactor, test, chore

# Example
git commit -m "feat: Add password reset functionality

- Implement email verification
- Add reset token generation
- Create password reset form

Closes #234"
```

### Commit Frequency

```bash
# ✅ Commit often (logical chunks)
git commit -m "Add login form HTML structure"
git commit -m "Add login form validation"
git commit -m "Add login form styling"

# ❌ Don't commit everything at once
git commit -m "Add entire login feature"

# ❌ Don't commit incomplete code to main
git commit -m "WIP: half done feature"  # Do this on feature branch only
```

### Branch Naming

```bash
# Good branch names
feature/user-authentication
feature/payment-integration
bugfix/login-redirect
hotfix/security-patch
refactor/database-queries
docs/api-documentation

# Include issue number
feature/123-add-shopping-cart
fix/456-memory-leak

# Use your initials for personal branches
jd/experiment-new-ui
```

### Keep Branches Updated

```bash
# Regularly update your branch with main
git checkout main
git pull
git checkout feature-branch
git rebase main              # Preferred
# or
git merge main               # Alternative

# Before creating PR
git fetch origin
git rebase origin/main
```

### Don't Commit These

```bash
# Add to .gitignore
node_modules/
.env
.env.local
*.log
.DS_Store
dist/
build/
coverage/
.vscode/
.idea/

# API keys, secrets, passwords
# Large files (use Git LFS instead)
# Compiled code (commit source only)
# IDE-specific files
```

### Protect Your Main Branch

```bash
# On GitHub: Settings → Branches → Add rule
# ✅ Require pull request reviews
# ✅ Require status checks to pass
# ✅ Require branches to be up to date
# ✅ Include administrators
# ❌ Allow force pushes (usually)
# ❌ Allow deletions (usually)
```

## Troubleshooting

### Merge Conflicts

```bash
# When conflict occurs
git merge feature-branch
# CONFLICT in file.txt

# 1. Open conflicted files
# Look for conflict markers:
<<<<<<< HEAD
current branch code
=======
incoming branch code
>>>>>>> feature-branch

# 2. Resolve conflicts manually
# Remove markers, keep desired code

# 3. Mark as resolved
git add file.txt

# 4. Complete merge
git commit
```

### Accidentally Committed to Main

```bash
# Move commit to new branch
git branch feature/my-changes
git reset --hard HEAD~1
git checkout feature/my-changes
```

### Forgot to Pull Before Making Changes

```bash
# You have local changes + remote has updates
git stash                    # Save local changes
git pull                     # Get remote changes
git stash pop                # Reapply local changes
# Resolve conflicts if any
```

### Need to Change Last Commit

```bash
# Change commit message
git commit --amend -m "New message"

# Add forgotten files to last commit
git add forgotten-file.txt
git commit --amend --no-edit

# ⚠️ Only amend commits that haven't been pushed!
```

### Pushed Wrong Files

```bash
# Remove file from Git but keep locally
git rm --cached sensitive-file.txt
echo "sensitive-file.txt" >> .gitignore
git add .gitignore
git commit -m "Remove sensitive file from tracking"
git push

# File is now ignored but remains on your computer
```

### Preventing Files from Being Committed (Beyond .gitignore)

Sometimes you need to ignore files locally without sharing those rules with your team, or you want extra safety nets to prevent sensitive files from being committed or deployed.

#### Method 1: Local Ignore with .git/info/exclude (Recommended for Personal Files)

The `.git/info/exclude` file works exactly like `.gitignore`, but it's **strictly local** to your machine and never committed to the repository.

**Use this when:**
- You have personal config files (`.claude.json`, `.vscode/settings.json`, etc.)
- You don't want to clutter team's `.gitignore` with personal preferences
- You want to ignore files without affecting other developers

**Step 1: Edit the exclude file**

```bash
# Navigate to your project
cd your-project

# Edit the exclude file (create if doesn't exist)
nano .git/info/exclude
# or
code .git/info/exclude
```

**Step 2: Add your exclusion rules**

```bash
# .git/info/exclude

# Personal config files
.claude.json
.claude/

# Personal IDE settings
.vscode/settings.json
.idea/workspace.xml

# Personal notes
NOTES.md
TODO.txt

# Personal test files
test-*.js
scratch/

# OS-specific (if not in .gitignore)
.DS_Store
Thumbs.db
```

**Step 3: Verify it works**

```bash
# Check status (excluded files won't appear)
git status

# Force check if file is ignored
git check-ignore -v .claude.json
# Output: .git/info/exclude:1:.claude.json	.claude.json
```

**Important notes:**

```bash
# This only affects NEW files
# If file is already tracked, you must untrack it first:
git rm --cached .claude.json
git commit -m "Remove .claude.json from tracking"

# Then add to .git/info/exclude
echo ".claude.json" >> .git/info/exclude

# Now the file is ignored locally but stays on your machine
```

#### Method 2: Global Gitignore (For All Your Repos)

Create a global ignore file for patterns you never want to commit in any project:

```bash
# Create global gitignore file
touch ~/.gitignore_global

# Configure Git to use it
git config --global core.excludesfile ~/.gitignore_global

# Edit the file
cat >> ~/.gitignore_global << 'EOF'
# Personal files
.claude.json
.claude/
.cursor/
.aider*

# OS files
.DS_Store
Thumbs.db
Desktop.ini

# Editor files
*.swp
*.swo
*~
.vscode/settings.json
.idea/workspace.xml

# Personal notes
NOTES.md
SCRATCH.md
TODO-personal.md
EOF

# Verify global config
git config --global core.excludesfile
# Output: /home/username/.gitignore_global
```

#### Method 3: Prevent Deployment with Platform-Specific Ignore Files

Even if files are in your repo, you can prevent them from being deployed:

**For Docker (.dockerignore)**

```bash
# Create .dockerignore in project root
cat > .dockerignore << 'EOF'
# Development files
.claude.json
.claude/
.env.local
.vscode/
.idea/

# Git files
.git/
.gitignore
.gitattributes

# Documentation
README.md
CONTRIBUTING.md
docs/

# Tests
tests/
*.test.js
*.spec.js
coverage/

# CI/CD
.github/
.gitlab-ci.yml

# Logs
*.log
npm-debug.log*

# Dependencies (will be installed fresh)
node_modules/
EOF

git add .dockerignore
git commit -m "Add .dockerignore for secure builds"
```

**For Vercel (.vercelignore)**

```bash
# Create .vercelignore
cat > .vercelignore << 'EOF'
# Development files
.claude.json
.claude/
.env.local
.env*.local

# Source control
.git/

# Dependencies
node_modules/

# Tests
tests/
__tests__/
*.test.ts
*.spec.ts

# Documentation
README.md
docs/
EOF

git add .vercelignore
git commit -m "Add .vercelignore"
```

**For Heroku (.slugignore)**

```bash
# Create .slugignore
cat > .slugignore << 'EOF'
# Development files
.claude.json
.claude/
.env.local

# Tests
tests/
*.test.js
spec/

# Documentation
README.md
CHANGELOG.md
docs/

# Git files
.git/
.gitignore
EOF

git add .slugignore
git commit -m "Add .slugignore"
```

**For Netlify (netlify.toml)**

```toml
# netlify.toml
[build]
  publish = "dist"
  command = "npm run build"

[build.environment]
  # Files to exclude from deployment
  ignore = [".claude.json", ".claude/", "*.local"]
```

#### Method 4: Pre-commit Hook (Hard Stop)

Create a Git hook that prevents you from accidentally committing sensitive files:

```bash
# Create pre-commit hook
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash

# Colors for output
RED='\033[0;31m'
NC='\033[0m' # No Color

# Files that should NEVER be committed
FORBIDDEN_FILES=(
    ".claude.json"
    ".claude"
    ".env.local"
    "secrets.json"
    "credentials.json"
)

# Check if any forbidden files are being staged
for file in "${FORBIDDEN_FILES[@]}"; do
    if git diff --cached --name-only | grep -q "$file"; then
        echo -e "${RED}ERROR: You are attempting to commit '$file'!${NC}"
        echo "This file should not be committed to the repository."
        echo "Add it to .git/info/exclude or .gitignore instead."
        exit 1
    fi
done

# Check for common secret patterns in staged files
if git diff --cached | grep -E "(api_key|secret|password|token).*=.*['\"][^'\"]{8,}['\"]"; then
    echo -e "${RED}WARNING: Possible secrets detected in staged files!${NC}"
    echo "Please review your changes carefully."
    read -p "Continue anyway? (y/N) " -n 1 -r
    echo
    if [[ ! $REPLY =~ ^[Yy]$ ]]; then
        exit 1
    fi
fi

exit 0
EOF

# Make it executable
chmod +x .git/hooks/pre-commit

# Test it
touch .claude.json
git add .claude.json
git commit -m "test"
# Output: ERROR: You are attempting to commit '.claude.json'!
```

#### Method 5: Git Hooks with Husky (Team-Wide Protection)

For team-wide protection, use Husky to share hooks:

```bash
# Install Husky
npm install --save-dev husky

# Initialize Husky
npx husky init

# Create pre-commit hook
cat > .husky/pre-commit << 'EOF'
#!/bin/bash

# Prevent committing sensitive files
FORBIDDEN_FILES=".claude.json|.env.local|secrets.json"

if git diff --cached --name-only | grep -E "$FORBIDDEN_FILES"; then
    echo "ERROR: Attempting to commit sensitive files!"
    echo "Files detected: $(git diff --cached --name-only | grep -E "$FORBIDDEN_FILES")"
    exit 1
fi

# Run linter/tests if needed
npm run lint
EOF

chmod +x .husky/pre-commit

# Commit the hook (shared with team)
git add .husky/pre-commit
git commit -m "Add pre-commit hook to prevent committing secrets"
```

#### Complete Example: Layered Protection Strategy

Here's how to set up multiple layers of protection:

```bash
# Layer 1: Local ignore (.git/info/exclude)
cat >> .git/info/exclude << 'EOF'
.claude.json
.claude/
.env.local
EOF

# Layer 2: Global ignore (all your projects)
cat >> ~/.gitignore_global << 'EOF'
.claude.json
.claude/
EOF

# Layer 3: Docker deployment protection
cat > .dockerignore << 'EOF'
.claude.json
.claude/
.env.local
.git/
EOF

# Layer 4: Pre-commit hook (hard stop)
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash
if git diff --cached --name-only | grep -q ".claude.json"; then
    echo "ERROR: Cannot commit .claude.json!"
    exit 1
fi
EOF
chmod +x .git/hooks/pre-commit

# Layer 5: If file was already committed, remove it
git rm --cached .claude.json 2>/dev/null || true
echo ".claude.json" >> .gitignore
git add .gitignore
git commit -m "Remove .claude.json from tracking and add to .gitignore" || true
```

#### Comparison: When to Use Each Method

| Method | Scope | Shared with Team | Use Case |
|--------|-------|------------------|----------|
| `.gitignore` | Repository | ✅ Yes | Team-wide ignored files (node_modules, .env) |
| `.git/info/exclude` | Repository | ❌ No | Personal files, local configs |
| `~/.gitignore_global` | All repos | ❌ No | Personal preferences across all projects |
| `.dockerignore` | Docker builds | ✅ Yes | Prevent files from entering container |
| `.vercelignore` | Vercel deploy | ✅ Yes | Prevent files from being deployed |
| Pre-commit hook (local) | Repository | ❌ No | Personal safety net |
| Pre-commit hook (Husky) | Repository | ✅ Yes | Team-wide safety net |

#### Troubleshooting

```bash
# File still appears in git status after adding to exclude
# Reason: File is already tracked
# Solution:
git rm --cached .claude.json
# Then it will be ignored

# Check which ignore rule is affecting a file
git check-ignore -v .claude.json
# Shows: .git/info/exclude:1:.claude.json	.claude.json

# Check if file is tracked
git ls-files .claude.json
# No output = not tracked (good!)
# Shows filename = tracked (need to untrack)

# Verify exclude file exists and is correct
cat .git/info/exclude

# Check global gitignore location
git config --global core.excludesfile

# Test pre-commit hook
# Create forbidden file
touch .claude.json
git add .claude.json
git commit -m "test"
# Should block if hook is working

# If hook isn't running, check executable permission
ls -la .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit
```

#### Best Practices

```bash
# 1. Remove file from tracking first, THEN ignore it
git rm --cached sensitive-file.txt
echo "sensitive-file.txt" >> .git/info/exclude
git commit -m "Remove sensitive file from tracking"

# 2. Use .git/info/exclude for personal files
echo ".claude.json" >> .git/info/exclude
echo ".vscode/settings.json" >> .git/info/exclude

# 3. Use .gitignore for team-wide patterns
echo "node_modules/" >> .gitignore
echo ".env" >> .gitignore
git add .gitignore
git commit -m "Update .gitignore"

# 4. Always use deployment ignore files
# Create .dockerignore, .vercelignore, etc.

# 5. Set up pre-commit hooks for critical files
# Especially for secrets, credentials, API keys

# 6. Document your ignore strategy in README
cat >> README.md << 'EOF'

## Local Development

Create a `.claude.json` file for AI assistance:
```json
{"api_key": "your-key"}
```

This file is ignored by Git (via .git/info/exclude) and will not be committed.
EOF
```

### Diverged Branches

```bash
# Error: Your branch and 'origin/main' have diverged

# Option 1: Rebase (cleaner history)
git pull --rebase origin main

# Option 2: Merge
git pull origin main

# Option 3: Force push (DANGEROUS - only on personal branches)
git push origin feature-branch --force-with-lease
```

### Lost Commits

```bash
# View all actions (even deleted commits)
git reflog

# Recover lost commit
git checkout commit-hash
git checkout -b recovered-branch
```

### Too Large Repository

```bash
# Find large files
git rev-list --objects --all |
  git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' |
  sort -n -k 3 |
  tail -20

# Use Git LFS for large files
git lfs install
git lfs track "*.psd"
git add .gitattributes
```

### GitHub Authentication Issues

```bash
# Test SSH connection
ssh -T git@github.com

# If fails, check SSH key
ls -la ~/.ssh
# Should see id_ed25519 and id_ed25519.pub

# Add key to SSH agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# Update remote URL to SSH
git remote set-url origin git@github.com:username/repo.git
```

### Line Ending Issues (CRLF vs LF)

#### Understanding Line Endings

Different operating systems use different line ending characters:
- **Windows**: CRLF (`\r\n`) - Carriage Return + Line Feed
- **Linux/macOS**: LF (`\n`) - Line Feed only

This can cause issues when collaborating across different operating systems.

#### Common CRLF Warning

```bash
# You might see this warning:
warning: CRLF will be replaced by LF in file.txt
The file will have its original line endings in your working directory
```

#### Solution 1: Configure Git Line Ending Behavior

```bash
# Windows users - convert to LF on commit, CRLF on checkout
git config --global core.autocrlf true

# Linux/macOS users - convert to LF on commit, keep LF on checkout
git config --global core.autocrlf input

# Disable automatic conversion (not recommended)
git config --global core.autocrlf false

# View current setting
git config core.autocrlf
```

#### Solution 2: Use .gitattributes (Recommended)

Create a `.gitattributes` file in your repository root to enforce consistent line endings across all contributors:

```bash
# Create .gitattributes file
cat > .gitattributes << 'EOF'
# Auto detect text files and normalize line endings to LF
* text=auto

# Explicitly declare text files you want to always normalize and convert to LF
*.js text eol=lf
*.jsx text eol=lf
*.ts text eol=lf
*.tsx text eol=lf
*.json text eol=lf
*.css text eol=lf
*.scss text eol=lf
*.html text eol=lf
*.md text eol=lf
*.yml text eol=lf
*.yaml text eol=lf
*.xml text eol=lf
*.sh text eol=lf
*.bash text eol=lf

# Python files
*.py text eol=lf

# Configuration files
.gitignore text eol=lf
.gitattributes text eol=lf
.editorconfig text eol=lf
*.config text eol=lf

# Windows-specific files that should use CRLF
*.bat text eol=crlf
*.cmd text eol=crlf
*.ps1 text eol=crlf

# Binary files that should not be modified
*.png binary
*.jpg binary
*.jpeg binary
*.gif binary
*.ico binary
*.pdf binary
*.woff binary
*.woff2 binary
*.ttf binary
*.eot binary
*.zip binary
*.gz binary
*.tar binary
EOF

# Add and commit .gitattributes
git add .gitattributes
git commit -m "Add .gitattributes for consistent line endings"
```

#### Complete .gitattributes Template for Web Projects

```bash
# .gitattributes
# Handle line endings automatically for files detected as text
# and leave all files detected as binary untouched.
* text=auto

#
# The above will handle all files NOT found below
#

# Source code
*.bash text eol=lf
*.sh text eol=lf
*.js text eol=lf
*.jsx text eol=lf
*.ts text eol=lf
*.tsx text eol=lf
*.coffee text eol=lf
*.json text eol=lf
*.vue text eol=lf
*.py text eol=lf
*.rb text eol=lf
*.php text eol=lf
*.java text eol=lf
*.c text eol=lf
*.cpp text eol=lf
*.h text eol=lf

# Web files
*.html text eol=lf
*.css text eol=lf
*.scss text eol=lf
*.sass text eol=lf
*.less text eol=lf
*.xml text eol=lf
*.svg text eol=lf

# Documentation
*.md text eol=lf
*.txt text eol=lf
*.rst text eol=lf

# Configuration files
*.yml text eol=lf
*.yaml text eol=lf
*.toml text eol=lf
*.ini text eol=lf
*.cfg text eol=lf
*.config text eol=lf
.editorconfig text eol=lf
.gitattributes text eol=lf
.gitignore text eol=lf
Makefile text eol=lf
Dockerfile text eol=lf
docker-compose.yml text eol=lf
package.json text eol=lf
package-lock.json text eol=lf
yarn.lock text eol=lf
tsconfig.json text eol=lf

# Windows-specific files
*.bat text eol=crlf
*.cmd text eol=crlf
*.ps1 text eol=crlf

# Binary files (images)
*.png binary
*.jpg binary
*.jpeg binary
*.gif binary
*.ico binary
*.webp binary
*.svg binary
*.bmp binary

# Binary files (fonts)
*.woff binary
*.woff2 binary
*.ttf binary
*.eot binary
*.otf binary

# Binary files (archives)
*.zip binary
*.tar binary
*.gz binary
*.bz2 binary
*.7z binary
*.rar binary

# Binary files (documents)
*.pdf binary
*.doc binary
*.docx binary
*.xls binary
*.xlsx binary
*.ppt binary
*.pptx binary

# Binary files (media)
*.mp4 binary
*.mp3 binary
*.mov binary
*.avi binary
*.wav binary
*.flac binary

# Binary files (other)
*.exe binary
*.dll binary
*.so binary
*.dylib binary
```

#### Fix Existing Files with Wrong Line Endings

If you already have files with mixed line endings in your repository:

```bash
# Method 1: Normalize all files in repository
# WARNING: This will rewrite history, coordinate with your team!

# Save your changes
git add . -u
git commit -m "Preparing to normalize line endings"

# Add .gitattributes
git add .gitattributes
git commit -m "Add .gitattributes for line ending normalization"

# Remove all files from index
git rm --cached -r .

# Re-add all files (Git will normalize them)
git reset --hard

# Re-add files with normalization
git add .

# Commit normalized files
git commit -m "Normalize all line endings"

# Push changes
git push origin main

# Method 2: Normalize specific files only
git add --renormalize .
git commit -m "Normalize line endings"

# Method 3: Use dos2unix utility (Linux/macOS)
# Install dos2unix
sudo apt install dos2unix  # Ubuntu/Debian
brew install dos2unix      # macOS

# Convert specific file
dos2unix path/to/file.txt

# Convert all files in directory
find . -type f -name "*.js" -exec dos2unix {} \;

# For Windows, use unix2dos
unix2dos path/to/file.bat
```

#### Best Practices for Line Endings

```bash
# 1. Always create .gitattributes at project start
# Put it in your repository root and commit it first

# 2. Team coordination
# Ensure all team members have proper git config:
git config --global core.autocrlf input  # Linux/macOS
git config --global core.autocrlf true   # Windows

# 3. Editor configuration
# Create .editorconfig file
cat > .editorconfig << 'EOF'
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true

[*.{js,jsx,ts,tsx,json,css,scss,html,md}]
indent_style = space
indent_size = 2

[*.{py,java}]
indent_style = space
indent_size = 4

[*.md]
trim_trailing_whitespace = false

[Makefile]
indent_style = tab
EOF

# 4. IDE/Editor settings
# VS Code: Add to settings.json
{
  "files.eol": "\n",
  "files.insertFinalNewline": true,
  "files.trimTrailingWhitespace": true
}

# 5. Pre-commit hooks (optional)
# Create .git/hooks/pre-commit
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash
# Check for CRLF line endings
if git diff --cached --name-only | xargs file | grep CRLF; then
    echo "Error: Found CRLF line endings. Please fix before committing."
    exit 1
fi
EOF
chmod +x .git/hooks/pre-commit
```

#### Troubleshooting Line Ending Issues

```bash
# Check current line endings in a file
file path/to/file.txt

# View line endings visually
cat -A path/to/file.txt
# ^M indicates CRLF (carriage return)

# Check which files have CRLF
find . -type f -name "*.js" -exec file {} \; | grep CRLF

# Git status shows all files as modified after line ending fix
# This is normal! Commit the changes:
git add .
git commit -m "Normalize line endings"

# Ignore line ending changes in git diff
git diff --ignore-cr-at-eol

# If GitHub shows entire file changed after line ending fix
# This is expected. The next diff will show only real changes.
```

#### Quick Fix for Common Scenarios

```bash
# Scenario 1: Clone on Windows, push shows all files changed
# Fix:
git config core.autocrlf true
git rm --cached -r .
git reset --hard

# Scenario 2: Warning on every commit
# Fix: Add .gitattributes as shown above

# Scenario 3: Mixed line endings in one file
# Use your editor's "Convert line endings" feature
# VS Code: Click "CRLF/LF" in bottom right → Select "LF"
# Or use dos2unix:
dos2unix path/to/file.txt

# Scenario 4: Shell scripts not working on Linux (^M errors)
# Convert to LF:
dos2unix script.sh
# Or:
sed -i 's/\r$//' script.sh
```

---

## Quick Reference Cheat Sheet

```bash
# Setup
git config --global user.name "Name"
git config --global user.email "email"

# Start
git init                                  # Initialize repo
git clone <url>                           # Clone repo

# Daily workflow
git status                                # Check status
git add <file>                            # Stage file
git add .                                 # Stage all
git commit -m "message"                   # Commit
git push                                  # Push to remote
git pull                                  # Pull from remote

# Branching
git branch                                # List branches
git branch <name>                         # Create branch
git checkout <name>                       # Switch branch
git checkout -b <name>                    # Create and switch
git merge <branch>                        # Merge branch
git branch -d <name>                      # Delete branch

# Undo
git checkout -- <file>                    # Discard changes
git reset <file>                          # Unstage
git reset --soft HEAD~1                   # Undo commit (keep changes)
git reset --hard HEAD~1                   # Undo commit (discard)
git revert <commit>                       # Revert commit

# Info
git log                                   # View history
git log --oneline --graph                 # Visual history
git diff                                  # View changes
git remote -v                             # View remotes

# Advanced
git stash                                 # Save changes temporarily
git stash pop                             # Restore stashed changes
git rebase <branch>                       # Rebase
git cherry-pick <commit>                  # Apply specific commit
git reflog                                # View all actions
```

---

[← Back to Main Guide](../README.md)
