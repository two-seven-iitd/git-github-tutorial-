# The Complete Git & GitHub Tutorial

> From zero to confident — every command you'll actually use, explained with examples.

---

## Table of Contents

1. [What is Git? What is GitHub?](#1-what-is-git-what-is-github)
2. [Installation & Setup](#2-installation--setup)
3. [Core Concepts](#3-core-concepts)
4. [Starting a Project](#4-starting-a-project)
5. [The Basic Workflow (add → commit → push)](#5-the-basic-workflow)
6. [Branching](#6-branching)
7. [Merging & Handling Conflicts](#7-merging--handling-conflicts)
8. [Remote Repositories (GitHub)](#8-remote-repositories-github)
9. [Undoing Things](#9-undoing-things)
10. [Stashing](#10-stashing)
11. [Viewing History & Diffs](#11-viewing-history--diffs)
12. [Tagging](#12-tagging)
13. [Rebasing](#13-rebasing)
14. [Cherry-Picking](#14-cherry-picking)
15. [GitHub-Specific Workflows](#15-github-specific-workflows)
16. [.gitignore](#16-gitignore)
17. [Git Aliases (Shortcuts)](#17-git-aliases-shortcuts)
18. [Common Scenarios & Recipes](#18-common-scenarios--recipes)
19. [Quick Reference Cheat Sheet](#19-quick-reference-cheat-sheet)

---

## 1. What is Git? What is GitHub?

**Git** is a distributed version control system. It tracks changes to your files over time, lets you revert to previous states, work on features in parallel via branches, and collaborate without overwriting each other's work. Every developer's machine holds the full history of the project.

**GitHub** is a cloud platform that hosts Git repositories. It adds collaboration features on top of Git: pull requests, issues, code reviews, actions (CI/CD), project boards, and more. Alternatives include GitLab, Bitbucket, and Azure DevOps — but GitHub is the most widely used.

**Key difference:** Git is the tool (runs locally). GitHub is the service (runs in the cloud).

---

## 2. Installation & Setup

### Install Git

| OS | Command |
|---|---|
| **Ubuntu/Debian** | `sudo apt update && sudo apt install git` |
| **Fedora** | `sudo dnf install git` |
| **macOS** | `brew install git` (or install Xcode Command Line Tools) |
| **Windows** | Download from [git-scm.com](https://git-scm.com) or `winget install Git.Git` |

### Verify Installation

```bash
git --version
# Example output: git version 2.43.0
```

### First-Time Configuration

These settings are stored globally and attached to every commit you make.

```bash
# Set your identity (REQUIRED before your first commit)
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Set default branch name to 'main' (modern standard)
git config --global init.defaultBranch main

# Set default editor (for commit messages, interactive rebase, etc.)
git config --global core.editor "code --wait"    # VS Code
# git config --global core.editor "vim"           # Vim
# git config --global core.editor "nano"          # Nano

# Enable colored output
git config --global color.ui auto

# View all your settings
git config --list

# View a specific setting
git config user.name
```

### Config Levels

| Level | Flag | File Location | Scope |
|---|---|---|---|
| System | `--system` | `/etc/gitconfig` | All users on the machine |
| Global | `--global` | `~/.gitconfig` | All repos for your user |
| Local | `--local` | `.git/config` in repo | Current repo only |

Local overrides Global, which overrides System.

---

## 3. Core Concepts

Understanding these three areas is the key to understanding Git:

```
 Working Directory       Staging Area (Index)       Local Repository
┌──────────────────┐    ┌──────────────────┐    ┌──────────────────┐
│                  │    │                  │    │                  │
│  Your actual     │    │  Changes ready   │    │  Committed       │
│  project files   │──▶│  to be committed │──▶│  snapshots       │
│                  │    │                  │    │  (history)       │
│                  │    │                  │    │                  │
└──────────────────┘    └──────────────────┘    └──────────────────┘
       git add               git commit              git push ──▶ Remote
```

- **Working Directory** — the files you see and edit on your disk.
- **Staging Area (Index)** — a holding zone. You pick which changes go into the next commit.
- **Repository (.git)** — the full history of all commits, branches, and tags.
- **Remote** — a copy of the repo hosted elsewhere (e.g., GitHub).

**File states:** Untracked → Unmodified → Modified → Staged → Committed.

---

## 4. Starting a Project

### Option A: Create a new repository from scratch

```bash
mkdir my-project
cd my-project
git init
# Output: Initialized empty Git repository in /path/my-project/.git/
```

This creates a hidden `.git/` folder that contains the entire version history.

### Option B: Clone an existing repository from GitHub

```bash
# Using HTTPS
git clone https://github.com/username/repo-name.git

# Using SSH (if you've set up SSH keys)
git clone git@github.com:username/repo-name.git

# Clone into a specific folder name
git clone https://github.com/username/repo-name.git my-folder

# Shallow clone (only latest commit — faster for huge repos)
git clone --depth 1 https://github.com/username/repo-name.git
```

---

## 5. The Basic Workflow

This is the cycle you'll repeat thousands of times:

### Step 1: Check the status of your repo

```bash
git status
```

This shows you which files are untracked, modified, or staged. Use it constantly.

```bash
git status -s    # Short/compact format
# Output examples:
#  M  file.txt      (modified, not staged)
# M   file.txt      (modified, staged)
# A   newfile.txt   (new file, staged)
# ??  random.txt    (untracked)
# MM  file.txt      (staged AND has new unstaged changes)
```

### Step 2: Stage changes (add to the staging area)

```bash
# Stage a specific file
git add filename.txt

# Stage multiple files
git add file1.txt file2.txt file3.txt

# Stage all changes in the current directory and subdirectories
git add .

# Stage all changes in the entire repo
git add -A

# Stage parts of a file interactively (pick specific hunks)
git add -p filename.txt

# Stage all tracked files that have been modified (ignores untracked)
git add -u
```

### Step 3: Commit (save a snapshot)

```bash
# Commit with an inline message
git commit -m "Add user login feature"

# Commit with a detailed message (opens your editor)
git commit

# Stage all tracked modified files AND commit in one step
git commit -am "Fix typo in README"
# Note: this does NOT add untracked (new) files

# Amend the last commit (fix message or add forgotten files)
git add forgotten-file.txt
git commit --amend -m "Updated commit message"
# Warning: don't amend commits that have been pushed
```

#### Writing Good Commit Messages

```
feat: add email verification on signup        ← subject line (imperative, <50 chars)
                                               ← blank line
Users now receive a verification email after   ← body (wrap at 72 chars)
registering. Clicking the link activates       
their account. Unverified accounts are         
auto-deleted after 24 hours.                   
                                               
Closes #42                                     ← footer (issue references)
```

Common prefixes (Conventional Commits): `feat:`, `fix:`, `docs:`, `style:`, `refactor:`, `test:`, `chore:`.

### Step 4: Push to GitHub

```bash
# Push to the remote (first time — sets upstream tracking)
git push -u origin main

# Subsequent pushes (tracking already set)
git push
```

### Step 5: Pull latest changes from GitHub

```bash
# Fetch + merge in one step
git pull

# Fetch + rebase instead of merge (cleaner history)
git pull --rebase

# Just fetch (download changes without merging)
git fetch
```

---

## 6. Branching

Branches let you work on features, fixes, or experiments in isolation.

### Creating & Switching Branches

```bash
# List all local branches (* marks the current one)
git branch

# List all branches including remote
git branch -a

# List remote branches only
git branch -r

# Create a new branch
git branch feature-login

# Switch to a branch
git checkout feature-login
# OR (modern, preferred)
git switch feature-login

# Create AND switch in one step
git checkout -b feature-login
# OR
git switch -c feature-login

# Create a branch from a specific commit or tag
git checkout -b hotfix abc1234
git checkout -b release-v2 v2.0.0
```

### Renaming & Deleting Branches

```bash
# Rename current branch
git branch -m new-name

# Rename a specific branch
git branch -m old-name new-name

# Delete a branch (only if fully merged)
git branch -d feature-login

# Force delete (even if not merged — use with caution)
git branch -D feature-login

# Delete a remote branch
git push origin --delete feature-login
```

### Typical Branch Naming Conventions

```
feature/user-authentication
bugfix/login-crash
hotfix/security-patch
release/v2.1.0
docs/update-readme
```

---

## 7. Merging & Handling Conflicts

### Merging

```bash
# First, switch to the branch you want to merge INTO
git checkout main

# Merge the feature branch into main
git merge feature-login

# Merge with a commit message (no fast-forward)
git merge --no-ff feature-login -m "Merge feature-login into main"

# Abort a merge if things go wrong
git merge --abort
```

**Fast-forward merge** — if main hasn't diverged, Git just moves the pointer forward. No merge commit.
**Three-way merge** — if both branches have new commits, Git creates a merge commit combining them.

### Handling Merge Conflicts

When Git can't auto-merge, it marks the file with conflict markers:

```
<<<<<<< HEAD
This is the content from the current branch (main).
=======
This is the content from the incoming branch (feature-login).
>>>>>>> feature-login
```

**To resolve:**

1. Open the file and decide which version to keep (or combine both).
2. Remove the `<<<<<<<`, `=======`, and `>>>>>>>` markers.
3. Stage the resolved file: `git add filename.txt`
4. Complete the merge: `git commit`

```bash
# See which files have conflicts
git status

# Use a merge tool (if configured)
git mergetool
```

---

## 8. Remote Repositories (GitHub)

### Managing Remotes

```bash
# View remotes
git remote -v

# Add a remote
git remote add origin https://github.com/username/repo.git

# Change remote URL (e.g., HTTPS to SSH)
git remote set-url origin git@github.com:username/repo.git

# Remove a remote
git remote remove origin

# Rename a remote
git remote rename origin upstream
```

### Fetching & Pulling

```bash
# Fetch all branches from remote (downloads but doesn't merge)
git fetch origin

# Fetch a specific branch
git fetch origin feature-login

# Pull = fetch + merge
git pull origin main

# Pull with rebase (avoids unnecessary merge commits)
git pull --rebase origin main
```

### Pushing

```bash
# Push current branch to remote
git push origin main

# Push and set upstream tracking (first push of a new branch)
git push -u origin feature-login

# Push all branches
git push --all origin

# Push tags
git push --tags

# Force push (DANGEROUS — rewrites remote history)
git push --force
# Safer version: fails if someone else pushed in the meantime
git push --force-with-lease
```

### Tracking Remote Branches

```bash
# Check out a remote branch and track it locally
git checkout -b feature-login origin/feature-login
# OR (shorthand, auto-creates local branch)
git checkout feature-login

# Set upstream for an existing local branch
git branch --set-upstream-to=origin/feature-login feature-login
# Shorthand
git branch -u origin/feature-login

# See tracking info
git branch -vv
```

---

## 9. Undoing Things

This is where Git truly shines. Multiple levels of "undo" depending on what you need.

### Unstage a File (undo `git add`)

```bash
# Modern way
git restore --staged filename.txt

# Old way (still works)
git reset HEAD filename.txt

# Unstage everything
git restore --staged .
```

### Discard Changes in Working Directory

```bash
# Discard changes to a specific file (CANNOT be undone)
git restore filename.txt

# Old way
git checkout -- filename.txt

# Discard ALL uncommitted changes
git restore .
```

### Undo Commits

```bash
# Undo last commit but KEEP changes staged
git reset --soft HEAD~1

# Undo last commit, KEEP changes in working directory (unstaged)
git reset HEAD~1
# OR
git reset --mixed HEAD~1

# Undo last commit and DISCARD all changes (DANGEROUS)
git reset --hard HEAD~1

# Undo last 3 commits
git reset --hard HEAD~3
```

### Revert a Commit (Safe for Shared Branches)

Unlike `reset`, `revert` creates a NEW commit that undoes the changes. Safe to use on pushed code.

```bash
# Revert a specific commit
git revert abc1234

# Revert the latest commit
git revert HEAD

# Revert without auto-committing (lets you edit first)
git revert --no-commit abc1234
```

### Reset vs Revert — When to Use Which

| Scenario | Command | Safe for shared branches? |
|---|---|---|
| Undo local unpushed commits | `git reset` | Yes (only local) |
| Undo pushed commits | `git revert` | Yes |
| Completely erase history | `git reset --hard` + force push | **No** (destructive) |

---

## 10. Stashing

Stash lets you temporarily save uncommitted changes and come back to them later.

```bash
# Stash current changes
git stash

# Stash with a description
git stash push -m "WIP: login form styling"

# Stash including untracked files
git stash -u

# Stash including untracked AND ignored files
git stash -a

# List all stashes
git stash list
# Output:
# stash@{0}: On main: WIP: login form styling
# stash@{1}: On feature: half-done API

# Apply the most recent stash (keeps it in stash list)
git stash apply

# Apply a specific stash
git stash apply stash@{1}

# Apply and remove from stash list
git stash pop

# Delete a specific stash
git stash drop stash@{0}

# Delete ALL stashes
git stash clear

# Show what's in a stash
git stash show stash@{0}
git stash show -p stash@{0}    # Full diff
```

**Common use case:** You're working on a feature but need to quickly switch branches to fix a bug. Stash your work, fix the bug, switch back, pop the stash.

---

## 11. Viewing History & Diffs

### Git Log

```bash
# Full log
git log

# Compact one-line format
git log --oneline

# With graph (shows branch structure)
git log --oneline --graph --all

# Decorated (shows branch/tag names)
git log --oneline --graph --all --decorate

# Last N commits
git log -5

# Log for a specific file
git log -- filename.txt

# Log with diffs (patches)
git log -p

# Log with stats (files changed, insertions, deletions)
git log --stat

# Search commit messages
git log --grep="login"

# Filter by author
git log --author="John"

# Filter by date
git log --since="2024-01-01" --until="2024-06-30"

# Show commits that changed a specific function/string
git log -S "functionName"
```

### Git Diff

```bash
# Changes in working directory (not yet staged)
git diff

# Changes that are staged (ready to commit)
git diff --staged
# OR
git diff --cached

# Diff between two branches
git diff main..feature-login

# Diff between two commits
git diff abc1234 def5678

# Diff for a specific file
git diff filename.txt

# Show only names of changed files
git diff --name-only

# Word-level diff (useful for prose)
git diff --word-diff
```

### Other History Commands

```bash
# Show details of a specific commit
git show abc1234

# Show who changed each line of a file (blame)
git blame filename.txt

# Shortened blame
git blame -s filename.txt

# Show reference log (all HEAD movements — lifesaver for recovery)
git reflog
```

---

## 12. Tagging

Tags mark specific points in history — typically used for releases.

```bash
# List all tags
git tag

# List tags matching a pattern
git tag -l "v1.*"

# Create a lightweight tag
git tag v1.0.0

# Create an annotated tag (recommended — stores metadata)
git tag -a v1.0.0 -m "Release version 1.0.0"

# Tag a specific past commit
git tag -a v0.9.0 abc1234 -m "Beta release"

# Show tag details
git show v1.0.0

# Push a specific tag to remote
git push origin v1.0.0

# Push ALL tags
git push --tags

# Delete a local tag
git tag -d v1.0.0

# Delete a remote tag
git push origin --delete v1.0.0
```

---

## 13. Rebasing

Rebasing replays your branch's commits on top of another branch. Creates a linear, clean history.

```bash
# Rebase current branch onto main
git checkout feature-login
git rebase main

# Interactive rebase — edit, squash, reorder, or drop commits
git rebase -i HEAD~4

# Interactive rebase onto main
git rebase -i main
```

### Interactive Rebase Commands

When you run `git rebase -i`, your editor opens with a list:

```
pick abc1234 Add login form
pick def5678 Fix typo
pick ghi9012 Add validation
pick jkl3456 More validation fixes
```

Change the commands:

| Command | Short | What It Does |
|---|---|---|
| `pick` | `p` | Keep the commit as-is |
| `reword` | `r` | Keep commit, edit the message |
| `edit` | `e` | Pause to amend the commit |
| `squash` | `s` | Merge into previous commit, combine messages |
| `fixup` | `f` | Merge into previous commit, discard this message |
| `drop` | `d` | Remove the commit entirely |
| `reorder` | — | Simply change the line order |

**Example — squash 4 commits into 1:**

```
pick abc1234 Add login form
fixup def5678 Fix typo
fixup ghi9012 Add validation
fixup jkl3456 More validation fixes
```

### Rebase vs Merge

| Aspect | Merge | Rebase |
|---|---|---|
| History | Preserves branch history (merge commits) | Linear, clean history |
| Safety | Safe for shared branches | **Never rebase shared/pushed branches** |
| Conflicts | Resolve once | May resolve for each replayed commit |
| Use case | Integrating feature → main | Keeping feature branch up to date |

### Abort or Continue a Rebase

```bash
# If conflicts arise during rebase
# 1. Fix the conflict in the file
# 2. Stage the fix
git add filename.txt
# 3. Continue
git rebase --continue

# Abort and go back to pre-rebase state
git rebase --abort

# Skip the current conflicting commit
git rebase --skip
```

---

## 14. Cherry-Picking

Apply a specific commit from another branch onto your current branch.

```bash
# Cherry-pick a single commit
git cherry-pick abc1234

# Cherry-pick without committing (stage the changes only)
git cherry-pick --no-commit abc1234

# Cherry-pick multiple commits
git cherry-pick abc1234 def5678

# Cherry-pick a range of commits
git cherry-pick abc1234..def5678

# Abort a cherry-pick
git cherry-pick --abort
```

---

## 15. GitHub-Specific Workflows

### Forking & Contributing (Open Source Workflow)

```bash
# 1. Fork the repo on GitHub (click the Fork button on the website)

# 2. Clone YOUR fork
git clone https://github.com/YOUR-USERNAME/repo.git
cd repo

# 3. Add the original repo as "upstream"
git remote add upstream https://github.com/ORIGINAL-OWNER/repo.git

# 4. Create a feature branch
git checkout -b fix-typo

# 5. Make changes, commit, and push to YOUR fork
git add .
git commit -m "Fix typo in README"
git push origin fix-typo

# 6. Go to GitHub and create a Pull Request from your fork to the original

# 7. Keep your fork in sync with upstream
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

### Pull Requests (PRs)

Pull Requests are GitHub's way of proposing changes. The workflow:

1. Create a branch and push it to GitHub.
2. On GitHub, click **"New Pull Request"**.
3. Select the base branch (e.g., `main`) and compare branch (your feature).
4. Add a title, description, reviewers, and labels.
5. Team reviews the code, leaves comments.
6. You push additional commits to the same branch to address feedback.
7. Once approved, merge the PR (merge commit, squash, or rebase).
8. Delete the branch.

#### Creating PRs from the CLI (GitHub CLI)

```bash
# Install GitHub CLI: https://cli.github.com
# Authenticate
gh auth login

# Create a PR
gh pr create --title "Add login feature" --body "Description here"

# Create a draft PR
gh pr create --draft

# List open PRs
gh pr list

# Check out a PR locally
gh pr checkout 42

# Merge a PR
gh pr merge 42

# View PR status
gh pr status
```

### GitHub Issues

```bash
# Create an issue
gh issue create --title "Bug: login fails" --body "Steps to reproduce..."

# List issues
gh issue list

# Close an issue
gh issue close 42

# Auto-close issues via commit messages
git commit -m "Fix crash on login. Closes #42"
# Keywords: Closes, Fixes, Resolves (+ issue number)
```

### SSH Key Setup for GitHub

```bash
# Generate an SSH key
ssh-keygen -t ed25519 -C "you@example.com"

# Start the SSH agent
eval "$(ssh-agent -s)"

# Add your key to the agent
ssh-add ~/.ssh/id_ed25519

# Copy the public key to clipboard
cat ~/.ssh/id_ed25519.pub
# Then paste it on GitHub → Settings → SSH and GPG keys → New SSH key

# Test the connection
ssh -T git@github.com
# Expected: "Hi username! You've successfully authenticated..."
```

---

## 16. .gitignore

The `.gitignore` file tells Git which files/folders to ignore. Create it in the root of your repo.

### Syntax

```gitignore
# Comments start with #

# Ignore a specific file
secrets.env

# Ignore all files with an extension
*.log
*.tmp
*.pyc

# Ignore a directory
node_modules/
__pycache__/
dist/
build/
.venv/

# Ignore files in any subdirectory
**/*.class

# Negate (don't ignore this specific file)
!important.log

# Ignore files only in the root directory
/TODO.txt

# Ignore everything inside a folder but keep the folder
logs/*
!logs/.gitkeep
```

### Common .gitignore Templates

**Node.js:**
```gitignore
node_modules/
.env
dist/
*.log
.DS_Store
```

**Python:**
```gitignore
__pycache__/
*.pyc
.venv/
.env
*.egg-info/
dist/
```

**Java:**
```gitignore
*.class
target/
*.jar
.idea/
*.iml
```

### Global .gitignore (for your machine)

```bash
# Create a global gitignore
git config --global core.excludesFile ~/.gitignore_global

# Add OS-specific files to it
echo ".DS_Store" >> ~/.gitignore_global     # macOS
echo "Thumbs.db" >> ~/.gitignore_global     # Windows
echo "*.swp" >> ~/.gitignore_global         # Vim swap files
```

### Remove Already-Tracked Files

If you added a file before adding it to `.gitignore`:

```bash
# Remove from tracking but keep the file on disk
git rm --cached filename.txt

# Remove an entire folder from tracking
git rm -r --cached node_modules/

# Then commit
git commit -m "Remove tracked files that should be ignored"
```

---

## 17. Git Aliases (Shortcuts)

```bash
# Set up aliases
git config --global alias.s "status -s"
git config --global alias.co "checkout"
git config --global alias.br "branch"
git config --global alias.ci "commit"
git config --global alias.lg "log --oneline --graph --all --decorate"
git config --global alias.last "log -1 HEAD"
git config --global alias.unstage "restore --staged"
git config --global alias.undo "reset HEAD~1 --mixed"
git config --global alias.amend "commit --amend --no-edit"

# Usage
git s          # instead of git status -s
git co main    # instead of git checkout main
git lg         # beautiful log graph
git undo       # undo last commit, keep changes
```

---

## 18. Common Scenarios & Recipes

### "I committed to the wrong branch"

```bash
# Move the last commit to the correct branch
git log --oneline -1                  # Note the commit hash
git reset HEAD~1                      # Undo on current branch (keeps changes)
git checkout correct-branch
git add .
git commit -m "Your message"
```

### "I need to update my feature branch with latest main"

```bash
# Option 1: Merge (preserves history)
git checkout feature-branch
git merge main

# Option 2: Rebase (cleaner history)
git checkout feature-branch
git rebase main
```

### "I want to see what changed in a PR before merging"

```bash
git fetch origin
git diff main..origin/feature-branch
git log main..origin/feature-branch --oneline
```

### "I accidentally deleted a branch"

```bash
# Find the last commit of the deleted branch
git reflog

# Recreate the branch from that commit
git checkout -b recovered-branch abc1234
```

### "I want to clean up untracked files"

```bash
# Dry run — see what would be deleted
git clean -n

# Delete untracked files
git clean -f

# Delete untracked files AND directories
git clean -fd

# Delete untracked + ignored files
git clean -fdx
```

### "I want to squash all commits before merging my PR"

```bash
# Count your commits on this branch
git log main..HEAD --oneline

# Interactive rebase to squash
git rebase -i main
# Change all but the first 'pick' to 'fixup', save, and close

# Force push (since you rewrote history)
git push --force-with-lease
```

---

## 19. Quick Reference Cheat Sheet

### Setup

| Command | Description |
|---|---|
| `git init` | Initialize a new repo |
| `git clone <url>` | Clone a remote repo |
| `git config --global user.name "Name"` | Set your name |
| `git config --global user.email "email"` | Set your email |

### Daily Workflow

| Command | Description |
|---|---|
| `git status` | Check current state |
| `git add <file>` | Stage a file |
| `git add .` | Stage all changes |
| `git commit -m "msg"` | Commit staged changes |
| `git push` | Push to remote |
| `git pull` | Pull from remote |
| `git fetch` | Download remote changes (no merge) |

### Branching

| Command | Description |
|---|---|
| `git branch` | List branches |
| `git branch <name>` | Create branch |
| `git switch <name>` | Switch branch |
| `git switch -c <name>` | Create + switch |
| `git branch -d <name>` | Delete branch |
| `git merge <branch>` | Merge branch into current |

### History

| Command | Description |
|---|---|
| `git log --oneline` | Compact log |
| `git log --graph --all` | Visual branch graph |
| `git diff` | Unstaged changes |
| `git diff --staged` | Staged changes |
| `git blame <file>` | Who changed each line |
| `git reflog` | All HEAD movements |

### Undoing

| Command | Description |
|---|---|
| `git restore <file>` | Discard working changes |
| `git restore --staged <file>` | Unstage |
| `git reset HEAD~1` | Undo last commit (keep changes) |
| `git reset --hard HEAD~1` | Undo last commit (discard changes) |
| `git revert <hash>` | Create undo commit (safe) |

### Remote

| Command | Description |
|---|---|
| `git remote -v` | List remotes |
| `git remote add origin <url>` | Add remote |
| `git push -u origin main` | First push with tracking |
| `git push --force-with-lease` | Safe force push |

### Stash

| Command | Description |
|---|---|
| `git stash` | Save changes temporarily |
| `git stash pop` | Restore + remove from stash |
| `git stash list` | List all stashes |
| `git stash drop` | Delete a stash |

### Tags

| Command | Description |
|---|---|
| `git tag v1.0.0` | Lightweight tag |
| `git tag -a v1.0.0 -m "msg"` | Annotated tag |
| `git push --tags` | Push all tags |

---

## Final Tips

1. **Commit often.** Small, focused commits are easier to review, revert, and understand.
2. **Pull before you push.** Always `git pull --rebase` before pushing to avoid unnecessary merge commits.
3. **Never force push to shared branches** unless everyone on the team is aware.
4. **Use `.gitignore` from day one.** Don't commit `node_modules`, `.env`, or build artifacts.
5. **Branch names should describe the work.** `feature/add-search` beats `my-branch`.
6. **Use `git reflog` when you panic.** It tracks everything and can help you recover almost anything.
7. **Learn interactive rebase.** `git rebase -i` is the most powerful tool for cleaning up history before merging.

---

*Created as a reference guide. Keep this file handy — you'll need it more than you think.*
