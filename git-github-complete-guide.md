# Complete Guide: Git Repository Setup and GitHub Workflow

## Table of Contents
1. [Initial Setup: Configuring Git on Your Local Machine](#initial-setup-configuring-git-on-your-local-machine)
2. [Method 1: Start with Local Repository](#method-1-start-with-local-repository)
3. [Method 2: Start with GitHub Repository](#method-2-start-with-github-repository)
4. [Method 3: Using GitHub CLI](#method-3-using-github-cli)
5. [Working with Branches](#working-with-branches)
6. [Creating Pull Requests](#creating-pull-requests)
7. [Complete Practical Example](#complete-practical-example)
8. [Common Workflows](#common-workflows)
9. [Troubleshooting](#troubleshooting)

---

## Initial Setup: Configuring Git on Your Local Machine

Before you start working with Git and GitHub, you need to configure your local machine. This is a **ONE-TIME SETUP**.

### Step 1: Install Git

**Check if Git is installed**:
```bash
git --version
```

**If not installed**:
- **macOS**: `brew install git` (requires Homebrew)
- **Windows**: Download from https://git-scm.com/download/win
- **Linux**: `sudo apt-get install git` (Ubuntu/Debian)

---

### Step 2: Configure Your Identity

This information appears in every commit you make.

```bash
# Set your name (can include spaces)
git config --global user.name "Oanh Nguyen"

# Set your email (use your GitHub email)
git config --global user.email "your.email@example.com"

# Verify configuration
git config --global --list
```

**Important**: Use the same email address that you'll use for GitHub.

---

### Step 3: Configure Default Branch Name

Modern Git uses `main` as the default branch name.

```bash
# Set default branch name to 'main'
git config --global init.defaultBranch main
```

---

### Step 4: Choose Your Authentication Method

You have **three options** for authenticating with GitHub. Choose ONE that works best for you.

---

## Authentication Method 1: GitHub CLI (EASIEST - RECOMMENDED)

### What You Need
- GitHub CLI installed

### Installation

**macOS**:
```bash
brew install gh
```

**Windows**:
```bash
winget install --id GitHub.cli
```

**Linux**:
```bash
# Debian/Ubuntu
sudo apt install gh

# Fedora
sudo dnf install gh
```

### Setup Steps

```bash
# Step 1: Login to GitHub
gh auth login

# You'll be prompted with questions:
# ? What account do you want to log into?
#   > GitHub.com

# ? What is your preferred protocol for Git operations?
#   > HTTPS

# ? Authenticate Git with your GitHub credentials?
#   > Yes

# ? How would you like to authenticate GitHub CLI?
#   > Login with a web browser

# Follow the browser prompt to authenticate
```

```bash
# Step 2: Configure Git to use GitHub CLI
gh auth setup-git
```

```bash
# Step 3: Verify authentication
gh auth status

# Expected output:
# github.com
#   ✓ Logged in to github.com account yourusername (keyring)
#   - Active account: true
#   - Git operations protocol: https
```

**Pros**:
- ✅ Easiest to set up
- ✅ No need to manage tokens or keys
- ✅ Works seamlessly with git commands
- ✅ Can create repos, PRs from command line

**Cons**:
- ❌ Requires installing additional software

---

## Authentication Method 2: SSH Keys (MOST SECURE)

### What You Need
- SSH key pair (public + private)

### Setup Steps

```bash
# Step 1: Check if you already have SSH keys
ls -la ~/.ssh

# Look for files like:
# id_rsa.pub (older)
# id_ed25519.pub (newer, recommended)
```

```bash
# Step 2: Generate new SSH key (if you don't have one)
ssh-keygen -t ed25519 -C "your.email@example.com"

# When prompted:
# Enter file in which to save the key (/Users/you/.ssh/id_ed25519):
# [Press Enter to accept default]

# Enter passphrase (empty for no passphrase):
# [Enter a secure passphrase or press Enter for none]
```

```bash
# Step 3: Start SSH agent
eval "$(ssh-agent -s)"

# Expected output:
# Agent pid 12345
```

```bash
# Step 4: Add SSH key to agent
ssh-add ~/.ssh/id_ed25519
```

```bash
# Step 5: Copy public key to clipboard

# macOS:
pbcopy < ~/.ssh/id_ed25519.pub

# Linux:
cat ~/.ssh/id_ed25519.pub
# Then manually copy the output

# Windows (Git Bash):
cat ~/.ssh/id_ed25519.pub | clip
```

```bash
# Step 6: Add SSH key to GitHub
# Go to: https://github.com/settings/keys
# Click "New SSH key"
# Title: "My MacBook" (or whatever you want)
# Key: Paste your public key
# Click "Add SSH key"
```

```bash
# Step 7: Test SSH connection
ssh -T git@github.com

# Expected output:
# Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

**Pros**:
- ✅ Most secure method
- ✅ No need to enter credentials after setup
- ✅ Industry standard

**Cons**:
- ❌ More complex initial setup
- ❌ Need to manage SSH keys
- ❌ Need to add key to each new machine

---

## Authentication Method 3: Personal Access Token (HTTPS)

### What You Need
- GitHub personal access token

### Setup Steps

```bash
# Step 1: Create Personal Access Token
# Go to: https://github.com/settings/tokens
# Click "Generate new token" → "Generate new token (classic)"

# Settings:
# - Note: "My MacBook" (or whatever you want)
# - Expiration: Choose duration
# - Select scopes:
#   ✓ repo (all)
#   ✓ workflow
#   ✓ write:packages
#   ✓ delete:packages

# Click "Generate token"
# COPY THE TOKEN - you won't see it again!
```

```bash
# Step 2: Configure Git credential helper

# macOS:
git config --global credential.helper osxkeychain

# Linux:
git config --global credential.helper cache
# OR for permanent storage:
git config --global credential.helper store

# Windows:
git config --global credential.helper manager
```

```bash
# Step 3: Use token when prompted
# Next time you push, Git will ask for credentials
# Username: your-github-username
# Password: paste-your-token-here
```

**Pros**:
- ✅ Works everywhere
- ✅ Can set expiration dates
- ✅ Can revoke without affecting other tokens

**Cons**:
- ❌ Need to save/store token securely
- ❌ Can expire (need to regenerate)
- ❌ Less convenient than SSH

---

## Method 1: Start with Local Repository

This method is used when you **already have code on your computer** and want to push it to GitHub.

### Step-by-Step Process

#### Step 1: Create Project Directory

```bash
# Create a new directory for your project
mkdir my-awesome-project

# Navigate into the directory
cd my-awesome-project
```

#### Step 2: Initialize Git Repository

```bash
# Initialize git in current directory
git init

# Expected output:
# Initialized empty Git repository in /path/to/my-awesome-project/.git/
```

**What this does**:
- Creates a hidden `.git` folder
- This folder contains all Git's tracking information
- Your directory is now a Git repository

#### Step 3: Create Some Files

```bash
# Create a README file
echo "# My Awesome Project" > README.md

# Create a sample code file
echo "console.log('Hello, World!');" > index.js

# Create a .gitignore file
cat > .gitignore << 'EOF'
node_modules/
.env
.DS_Store
*.log
EOF
```

**Understanding .gitignore**:
- Lists files/folders Git should ignore
- Prevents committing sensitive or unnecessary files
- Common examples: `node_modules/`, `.env`, log files

#### Step 4: Check Status

```bash
git status
```

**Expected output**:
```
On branch main

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        .gitignore
        README.md
        index.js

nothing added to commit but untracked files present (use "git add" to track)
```

**What this means**:
- You're on the `main` branch
- No commits exist yet
- Three files are untracked (Git doesn't know about them)

#### Step 5: Stage Files

```bash
# Add specific files
git add README.md
git add index.js

# OR add all files at once
git add .
```

**What staging means**:
- Preparing files to be committed
- Like putting items in a shopping cart before checkout
- You can stage some files and leave others unstaged

```bash
# Check status again
git status
```

**Expected output**:
```
On branch main

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   .gitignore
        new file:   README.md
        new file:   index.js
```

#### Step 6: Create First Commit

```bash
# Commit with a message
git commit -m "Initial commit: Add README and index.js"
```

**Expected output**:
```
[main (root-commit) a1b2c3d] Initial commit: Add README and index.js
 3 files changed, 10 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 README.md
 create mode 100644 index.js
```

**What happened**:
- Created a snapshot of your files
- Generated a unique commit ID (a1b2c3d)
- Saved with your message

**Commit Message Best Practices**:
- Use present tense: "Add feature" not "Added feature"
- Be concise but descriptive
- First line: summary (50 chars or less)
- Blank line, then detailed explanation if needed

#### Step 7: Create GitHub Repository

**Option A: Using GitHub CLI (Recommended)**

```bash
# Create public repository
gh repo create my-awesome-project --public --source=. --remote=origin

# OR create private repository
gh repo create my-awesome-project --private --source=. --remote=origin
```

**What this does**:
- Creates repository on GitHub
- Sets up `origin` remote
- Does NOT automatically push code

**Option B: Using GitHub Website**

1. Go to https://github.com/new
2. Repository name: `my-awesome-project`
3. Description: (optional) "My first project"
4. Choose Public or Private
5. **DO NOT** initialize with README, .gitignore, or license
6. Click "Create repository"

**Why NOT initialize**? Because we already have files locally.

#### Step 8: Connect Local to GitHub

If you used GitHub website, you need to add the remote manually:

```bash
# Add remote repository
git remote add origin https://github.com/yourusername/my-awesome-project.git

# If using SSH instead:
git remote add origin git@github.com:yourusername/my-awesome-project.git
```

```bash
# Verify remote was added
git remote -v
```

**Expected output**:
```
origin  https://github.com/yourusername/my-awesome-project.git (fetch)
origin  https://github.com/yourusername/my-awesome-project.git (push)
```

#### Step 9: Push to GitHub

```bash
# Push code to GitHub
git push -u origin main
```

**Breaking down the command**:
- `git push`: Upload commits to remote
- `-u`: Set upstream tracking (only needed first time)
- `origin`: The remote repository
- `main`: The branch name

**Expected output**:
```
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (5/5), 450 bytes | 450.00 KiB/s, done.
Total 5 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/yourusername/my-awesome-project.git
 * [new branch]      main -> main
Branch 'main' set up to track remote branch 'main' from 'origin'.
```

#### Step 10: Verify on GitHub

```bash
# Open repository in browser
gh repo view --web

# OR manually go to:
# https://github.com/yourusername/my-awesome-project
```

**Success!** Your code is now on GitHub! 🎉

---

## Method 2: Start with GitHub Repository

This method is used when you want to **start on GitHub first**, then download to your computer.

### Step-by-Step Process

#### Step 1: Create Repository on GitHub

**Using GitHub CLI**:
```bash
gh repo create my-new-project --public --clone

# OR for private:
gh repo create my-new-project --private --clone
```

**What `--clone` does**: Automatically creates the repository and clones it to your computer.

**Using GitHub Website**:
1. Go to https://github.com/new
2. Repository name: `my-new-project`
3. Description: (optional)
4. Choose Public or Private
5. ✅ **Check** "Add a README file"
6. Choose a .gitignore template (e.g., Node, Python, etc.)
7. Choose a license (e.g., MIT)
8. Click "Create repository"

#### Step 2: Clone Repository to Local Machine

```bash
# Navigate to where you want the project
cd ~/workspace

# Clone using HTTPS
git clone https://github.com/yourusername/my-new-project.git

# OR clone using SSH
git clone git@github.com:yourusername/my-new-project.git
```

**Expected output**:
```
Cloning into 'my-new-project'...
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), done.
```

```bash
# Navigate into the cloned repository
cd my-new-project
```

#### Step 3: Verify Clone

```bash
# Check files
ls -la

# Check remote
git remote -v

# Check current branch
git branch
```

#### Step 4: Make Changes

```bash
# Create a new file
echo "console.log('Hello from cloned repo!');" > app.js

# Check what changed
git status
```

#### Step 5: Stage, Commit, and Push

```bash
# Stage changes
git add app.js

# Commit
git commit -m "Add app.js with hello message"

# Push to GitHub
git push
```

**Note**: No need for `-u origin main` because the tracking is already set up when you cloned.

---

## Method 3: Using GitHub CLI (FASTEST)

The quickest way to create and push a repository.

### One-Command Setup

```bash
# Navigate to your project directory
cd my-existing-project

# Create repo and push in one command
gh repo create my-project --source=. --public --push
```

**What this does**:
1. Creates repository on GitHub
2. Initializes git locally (if not already initialized)
3. Adds remote
4. Pushes all commits

**Alternative: Interactive Mode**

```bash
gh repo create

# You'll be prompted:
# ? What would you like to do?
#   > Push an existing local repository to GitHub
#
# ? Path to local repository: .
# ? Repository name: my-project
# ? Description: My awesome project
# ? Visibility: Public
# ? Add a remote? Yes
# ? What should the new remote be called? origin
# ? Would you like to push commits from the current branch? Yes
```

---

## Working with Branches

Branches allow you to work on features without affecting the main codebase.

### Understanding Branches

```
main branch:      A --- B --- C --- D
                          \
feature branch:            E --- F
```

- `main`: Production-ready code
- `feature`: Experimental/in-progress work

### Creating and Using Branches

#### Create a New Branch

```bash
# Create and switch to new branch
git checkout -b feature/add-login

# OR (newer syntax)
git switch -c feature/add-login
```

**Branch naming conventions**:
- `feature/` - New features: `feature/user-authentication`
- `bugfix/` - Bug fixes: `bugfix/login-error`
- `hotfix/` - Urgent fixes: `hotfix/security-patch`
- `docs/` - Documentation: `docs/update-readme`

#### Check Current Branch

```bash
# See all branches (current marked with *)
git branch

# See all branches including remote
git branch -a
```

#### Make Changes on Branch

```bash
# Create a new file
echo "Login functionality" > login.js

# Stage and commit
git add login.js
git commit -m "Add login functionality"
```

#### Push Branch to GitHub

```bash
# Push new branch to GitHub
git push -u origin feature/add-login
```

**Expected output**:
```
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
remote:
remote: Create a pull request for 'feature/add-login' on GitHub by visiting:
remote:      https://github.com/yourusername/my-project/pull/new/feature/add-login
remote:
To https://github.com/yourusername/my-project.git
 * [new branch]      feature/add-login -> feature/add-login
Branch 'feature/add-login' set up to track remote branch 'feature/add-login' from 'origin'.
```

#### Switch Between Branches

```bash
# Switch to main branch
git checkout main
# OR
git switch main

# Switch back to feature branch
git checkout feature/add-login
# OR
git switch feature/add-login
```

#### List All Branches

```bash
# Local branches
git branch

# Remote branches
git branch -r

# All branches
git branch -a
```

#### Delete a Branch

```bash
# Delete local branch (after merging)
git branch -d feature/add-login

# Force delete local branch (if not merged)
git branch -D feature/add-login

# Delete remote branch
git push origin --delete feature/add-login
```

---

## Creating Pull Requests

A Pull Request (PR) is a request to merge your changes into another branch (usually `main`).

### Method 1: Using GitHub CLI (Easiest)

```bash
# Make sure you're on your feature branch
git checkout feature/add-login

# Create pull request
gh pr create
```

**You'll be prompted**:
```
? Where should we push the 'feature/add-login' branch?
  > yourusername/my-project

? Title: Add login functionality
? Body: <Received>
? What's next?
  > Submit
```

**Or create with flags**:
```bash
gh pr create --title "Add login functionality" --body "This PR adds user login with authentication"
```

**With more options**:
```bash
gh pr create \
  --title "Add login functionality" \
  --body "Implements user authentication using JWT tokens" \
  --base main \
  --head feature/add-login \
  --label "enhancement" \
  --reviewer username1,username2
```

### Method 2: Using GitHub Website

#### Step 1: Push Your Branch

```bash
git push -u origin feature/add-login
```

#### Step 2: Go to GitHub

After pushing, GitHub will show a message:
```
remote: Create a pull request for 'feature/add-login' on GitHub by visiting:
remote:      https://github.com/yourusername/my-project/pull/new/feature/add-login
```

#### Step 3: Click the Link or:

1. Go to your repository on GitHub
2. Click "Pull requests" tab
3. Click "New pull request"
4. Select branches:
   - **base**: `main` (where you want to merge TO)
   - **compare**: `feature/add-login` (what you want to merge FROM)

#### Step 4: Fill Out PR Form

```
Title: Add login functionality

Description:
## What does this PR do?
- Adds user login page
- Implements JWT authentication
- Adds password validation

## How to test?
1. Run `npm install`
2. Run `npm start`
3. Navigate to /login
4. Test with credentials: user@example.com / password123

## Screenshots
[Add if relevant]

## Checklist
- [x] Code follows style guidelines
- [x] Added tests
- [x] Updated documentation
```

#### Step 5: Create Pull Request

Click "Create pull request"

### Reviewing and Merging PRs

#### View Pull Request

```bash
# List all open PRs
gh pr list

# View specific PR
gh pr view 1

# View PR in browser
gh pr view 1 --web
```

#### Review PR Locally

```bash
# Checkout PR to test it locally
gh pr checkout 1

# Test the code
npm test

# Go back to main
git checkout main
```

#### Merge Pull Request

**Option 1: Using GitHub CLI**

```bash
# Merge PR
gh pr merge 1

# You'll be prompted:
# ? What merge method would you like to use?
#   > Create a merge commit
#     Squash and merge
#     Rebase and merge

# ? Delete the branch locally and on GitHub? Yes
```

**Option 2: Using GitHub Website**

1. Go to the PR page
2. Review changes
3. Click "Merge pull request"
4. Choose merge method:
   - **Create a merge commit**: Keeps all commits
   - **Squash and merge**: Combines all commits into one
   - **Rebase and merge**: Reapplies commits on top of base branch
5. Click "Confirm merge"
6. Optionally delete the branch

#### After Merging

```bash
# Switch to main branch
git checkout main

# Pull latest changes (includes the merge)
git pull origin main

# Delete local feature branch
git branch -d feature/add-login
```

---

## Complete Practical Example

Let's walk through a complete real-world scenario from start to finish.

### Scenario: Building a Todo App

#### Phase 1: Initial Setup

```bash
# Create project directory
mkdir todo-app
cd todo-app

# Initialize git
git init

# Create initial files
cat > README.md << 'EOF'
# Todo App

A simple todo application built with Node.js

## Features
- Add todos
- Mark todos as complete
- Delete todos
EOF

cat > package.json << 'EOF'
{
  "name": "todo-app",
  "version": "1.0.0",
  "description": "A simple todo application",
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  }
}
EOF

cat > app.js << 'EOF'
// Todo App
console.log('Todo App Starting...');

const todos = [];

function addTodo(task) {
  todos.push({ task, completed: false });
  console.log(`Added: ${task}`);
}

addTodo('Learn Git');
addTodo('Create GitHub repo');
EOF

cat > .gitignore << 'EOF'
node_modules/
.env
.DS_Store
*.log
EOF

# Check what we created
ls -la
```

#### Phase 2: First Commit

```bash
# Check status
git status

# Stage all files
git add .

# Commit
git commit -m "Initial commit: Setup todo app structure"

# View commit history
git log --oneline
```

#### Phase 3: Create GitHub Repository

```bash
# Create and push (using GitHub CLI)
gh repo create todo-app --source=. --public --push

# OR manually:
# 1. Go to https://github.com/new
# 2. Create repository
# 3. git remote add origin https://github.com/yourusername/todo-app.git
# 4. git push -u origin main
```

#### Phase 4: Add New Feature (Using Branches)

```bash
# Create feature branch
git checkout -b feature/delete-todo

# Add delete functionality to app.js
cat >> app.js << 'EOF'

function deleteTodo(index) {
  if (index >= 0 && index < todos.length) {
    const deleted = todos.splice(index, 1);
    console.log(`Deleted: ${deleted[0].task}`);
  }
}

deleteTodo(0);
console.log('Remaining todos:', todos);
EOF

# Check changes
git diff

# Stage and commit
git add app.js
git commit -m "Add delete todo functionality"

# Push feature branch
git push -u origin feature/delete-todo
```

#### Phase 5: Create Pull Request

```bash
# Create PR using GitHub CLI
gh pr create \
  --title "Add delete todo functionality" \
  --body "This PR adds the ability to delete todos by index.

## Changes
- Added deleteTodo() function
- Added example usage
- Updated console logs

## Testing
Run \`node app.js\` to test delete functionality" \
  --label "enhancement"

# View the PR
gh pr view --web
```

#### Phase 6: Review and Merge

```bash
# View PR status
gh pr status

# If tests pass and review is approved, merge
gh pr merge --squash --delete-branch

# Switch back to main
git checkout main

# Pull latest changes
git pull origin main

# View updated code
cat app.js
```

#### Phase 7: Add Another Feature

```bash
# Create another feature branch
git checkout -b feature/complete-todo

# Add complete functionality
cat >> app.js << 'EOF'

function completeTodo(index) {
  if (index >= 0 && index < todos.length) {
    todos[index].completed = true;
    console.log(`Completed: ${todos[index].task}`);
  }
}

// Test it
addTodo('Write documentation');
completeTodo(0);
console.log('All todos:', todos);
EOF

# Stage, commit, and push
git add app.js
git commit -m "Add complete todo functionality"
git push -u origin feature/complete-todo

# Create PR
gh pr create \
  --title "Add complete todo functionality" \
  --body "Adds ability to mark todos as complete" \
  --label "enhancement"
```

#### Phase 8: Handle Conflicts (If Any)

```bash
# If main has changed and conflicts arise
git checkout feature/complete-todo

# Update from main
git pull origin main

# If there are conflicts, Git will tell you
# Edit conflicted files
# Look for markers:
# <<<<<<< HEAD
# Your changes
# =======
# Changes from main
# >>>>>>> main

# After resolving
git add .
git commit -m "Resolve merge conflicts"
git push
```

#### Phase 9: Tag a Release

```bash
# After everything is merged
git checkout main
git pull origin main

# Create a tag
git tag -a v1.0.0 -m "First stable release"

# Push tag to GitHub
git push origin v1.0.0

# OR create release using GitHub CLI
gh release create v1.0.0 \
  --title "Version 1.0.0" \
  --notes "First stable release with add, delete, and complete functionality"
```

---

## Common Workflows

### Daily Development Workflow

```bash
# Morning: Start work
git checkout main
git pull origin main

# Create feature branch
git checkout -b feature/new-feature

# Work on code...
# (make changes)

# Save work
git add .
git commit -m "Implement new feature"

# Push to GitHub
git push -u origin feature/new-feature

# Create PR when ready
gh pr create

# After PR is merged
git checkout main
git pull origin main
git branch -d feature/new-feature
```

### Updating Your Branch with Latest Main

```bash
# You're on feature/my-feature
git checkout main
git pull origin main
git checkout feature/my-feature
git merge main

# OR use rebase (cleaner history)
git checkout feature/my-feature
git rebase main

# If conflicts occur, resolve them
# Then continue
git rebase --continue

# Force push (only if you've already pushed the branch)
git push --force-with-lease
```

### Stashing Changes (Save Work Temporarily)

```bash
# You're working but need to switch branches
git stash

# OR with a message
git stash save "Work in progress on login"

# Switch branches and do other work
git checkout main

# Come back to your branch
git checkout feature/my-feature

# Restore your work
git stash pop

# List all stashes
git stash list

# Apply specific stash
git stash apply stash@{0}
```

### Undoing Changes

```bash
# Undo changes to a file (not staged)
git checkout -- filename.js

# Unstage a file (but keep changes)
git reset HEAD filename.js

# Undo last commit (keep changes)
git reset --soft HEAD~1

# Undo last commit (discard changes) - DANGEROUS!
git reset --hard HEAD~1

# Revert a commit (creates new commit)
git revert abc123

# Amend last commit message
git commit --amend -m "New message"

# Amend last commit (add more files)
git add forgotten-file.js
git commit --amend --no-edit
```

### Viewing History

```bash
# View commit history
git log

# Compact view
git log --oneline

# With graph
git log --oneline --graph --all

# View changes in each commit
git log -p

# View commits by author
git log --author="Oanh"

# View commits in date range
git log --since="2 weeks ago"

# View file history
git log -- filename.js

# View what changed in a commit
git show abc123
```

### Working with Remote

```bash
# View remote repositories
git remote -v

# Add new remote
git remote add upstream https://github.com/original/repo.git

# Fetch from remote (doesn't merge)
git fetch origin

# Pull from remote (fetch + merge)
git pull origin main

# Push to remote
git push origin feature-branch

# Delete remote branch
git push origin --delete feature-branch

# Update remote tracking branches
git remote update origin --prune
```

---

## Troubleshooting

### Problem: "fatal: not a git repository"

```bash
# You're not in a git repository
# Solution: Navigate to project or initialize
git init
```

### Problem: "fatal: remote origin already exists"

```bash
# Solution: Remove and re-add
git remote remove origin
git remote add origin https://github.com/username/repo.git
```

### Problem: "Permission denied (publickey)"

```bash
# SSH key not set up
# Solution: Use HTTPS instead or set up SSH keys
git remote set-url origin https://github.com/username/repo.git
```

### Problem: "failed to push some refs"

```bash
# Remote has commits you don't have
# Solution: Pull first
git pull origin main --rebase
git push origin main
```

### Problem: "Your branch is ahead of 'origin/main' by X commits"

```bash
# You have local commits not pushed
# Solution: Push them
git push origin main
```

### Problem: "Your branch is behind 'origin/main' by X commits"

```bash
# Remote has commits you don't have
# Solution: Pull them
git pull origin main
```

### Problem: Merge Conflicts

```bash
# After git pull or git merge, you see conflicts
# Solution:
# 1. Open conflicted files
# 2. Look for conflict markers:
#    <<<<<<< HEAD
#    Your changes
#    =======
#    Their changes
#    >>>>>>> branch-name
# 3. Edit to resolve
# 4. Remove markers
# 5. Save file

git add conflicted-file.js
git commit -m "Resolve merge conflict"
```

### Problem: Accidentally committed to wrong branch

```bash
# You committed to main instead of feature branch
# Solution:
git checkout main
git reset --soft HEAD~1  # Undo commit, keep changes
git stash                # Save changes
git checkout -b feature/correct-branch
git stash pop            # Restore changes
git add .
git commit -m "Correct commit message"
```

### Problem: Need to undo a public commit

```bash
# Don't use reset on public branches!
# Solution: Use revert
git revert abc123
git push origin main
```

---

## Quick Reference Cheat Sheet

### Setup Commands
```bash
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
git init
gh auth login
gh auth setup-git
```

### Basic Commands
```bash
git status              # Check status
git add .               # Stage all changes
git add file.js         # Stage specific file
git commit -m "msg"     # Commit with message
git push                # Push to remote
git pull                # Pull from remote
git clone <url>         # Clone repository
```

### Branch Commands
```bash
git branch              # List branches
git branch name         # Create branch
git checkout name       # Switch to branch
git checkout -b name    # Create and switch
git merge name          # Merge branch
git branch -d name      # Delete branch
```

### Remote Commands
```bash
git remote -v           # View remotes
git remote add origin <url>  # Add remote
git push -u origin main # Push and track
git fetch origin        # Fetch changes
```

### GitHub CLI Commands
```bash
gh repo create          # Create repository
gh repo view --web      # Open in browser
gh pr create            # Create pull request
gh pr list              # List PRs
gh pr merge             # Merge PR
gh pr checkout <num>    # Checkout PR locally
```

### Undo Commands
```bash
git checkout -- file    # Discard changes
git reset HEAD file     # Unstage file
git reset --soft HEAD~1 # Undo commit
git revert <commit>     # Revert commit
git stash               # Stash changes
git stash pop           # Restore stash
```

---

## Best Practices Summary

1. **Commit Often**: Small, frequent commits are better than large ones
2. **Write Good Messages**: Clear, descriptive commit messages help everyone
3. **Use Branches**: Never commit directly to main for features
4. **Pull Before Push**: Always pull latest changes before pushing
5. **Review PRs**: Always review code before merging
6. **Delete Merged Branches**: Keep repository clean
7. **Use .gitignore**: Don't commit unnecessary files
8. **Tag Releases**: Mark important milestones with tags
9. **Sync Regularly**: Pull from main often to avoid conflicts
10. **Back Up Work**: Push your branches regularly

---

## Additional Resources

- **Official Git Documentation**: https://git-scm.com/doc
- **GitHub Guides**: https://guides.github.com/
- **GitHub CLI Manual**: https://cli.github.com/manual/
- **Interactive Git Tutorial**: https://learngitbranching.js.org/
- **Git Cheat Sheet**: https://education.github.com/git-cheat-sheet-education.pdf

---

## Conclusion

You now have a complete guide to:
- ✅ Configure Git on your local machine
- ✅ Create repositories (local-first or GitHub-first)
- ✅ Work with branches
- ✅ Create and merge pull requests
- ✅ Handle common workflows
- ✅ Troubleshoot issues

**Remember**: Practice makes perfect. Start with small projects and gradually tackle more complex workflows. Don't be afraid to experiment - Git makes it hard to permanently lose work!

Happy coding! 🚀
