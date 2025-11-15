# Git Setup and Use Guide with Shortcuts

## Initial Setup

### Installing Git

**macOS:**
```bash
brew install git
```

**Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install git
```

**Windows:**
Download from [git-scm.com](https://git-scm.com/download/win)

### First-Time Configuration

```bash
# Set your identity
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Set default branch name
git config --global init.defaultBranch main

# Enable helpful colorization
git config --global color.ui auto

# Set default editor (choose one)
git config --global core.editor "vim"
git config --global core.editor "nano"
git config --global core.editor "code --wait"  # VS Code
```

## Essential Git Aliases (Shortcuts)

Add these to your `~/.gitconfig` file under `[alias]` section, or use the commands below:

```bash
# Status shortcuts
git config --global alias.st status
git config --global alias.s "status -s"  # Short status

# Branch shortcuts
git config --global alias.br branch
git config --global alias.ba "branch -a"  # All branches

# Commit shortcuts
git config --global alias.ci commit
git config --global alias.cm "commit -m"  # Commit with message
git config --global alias.ca "commit --amend"  # Amend last commit

# Checkout shortcuts
git config --global alias.co checkout
git config --global alias.cob "checkout -b"  # Create and checkout branch

# Log shortcuts
git config --global alias.lg "log --oneline --graph --decorate --all"
git config --global alias.last "log -1 HEAD"  # Show last commit

# Diff shortcuts
git config --global alias.df diff
git config --global alias.dfs "diff --staged"

# Other useful shortcuts
git config --global alias.unstage "reset HEAD --"
git config --global alias.undo "reset --soft HEAD~1"
git config --global alias.aliases "config --get-regexp alias"
```

## Common Workflows

### Starting a New Repository

```bash
# Initialize a new repository
git init

# Or clone an existing repository
git clone https://github.com/username/repo.git
```

### Basic Workflow

```bash
# Check status
git st  # or git status

# Add files to staging
git add filename.txt
git add .  # Add all changes

# Commit changes
git -m "Your commit message"

# Push to remote
git push origin main
```

### Working with Branches

```bash
# Create a new branch
git cob feature-name  # or git checkout -b feature-name

# Switch branches
git co main  # or git checkout main

# List all branches
git ba  # or git branch -a

# Delete a branch
git branch -d feature-name  # Safe delete
git branch -D feature-name  # Force delete

# Merge a branch
git co main
git merge feature-name
```

### Pulling and Pushing

```bash
# Pull latest changes
git pull origin main

# Pull with rebase
git pull --rebase origin main

# Push changes
git push origin main

# Push new branch
git push -u origin feature-name

# Force push (use with caution!)
git push --force-with-lease origin main
```

### Viewing History

```bash
# Pretty log with graph
git lg

# View last commit
git last

# View specific number of commits
git log -n 5

# Search commits
git log --grep="keyword"

# View changes by author
git log --author="Your Name"
```

### Undoing Changes

```bash
# Unstage a file
git unstage filename.txt

# Discard changes in working directory
git checkout -- filename.txt
git restore filename.txt  # Git 2.23+

# Undo last commit (keep changes)
git undo  # or git reset --soft HEAD~1

# Undo last commit (discard changes)
git reset --hard HEAD~1

# Revert a specific commit
git revert <commit-hash>
```

### Stashing Changes

```bash
# Stash current changes
git stash
git stash save "Work in progress on feature X"

# List stashes
git stash list

# Apply most recent stash
git stash apply
git stash pop  # Apply and remove

# Apply specific stash
git stash apply stash@{2}

# Clear all stashes
git stash clear
```

## Advanced Shortcuts and Tricks

### Interactive Rebase

```bash
# Rebase last 3 commits
git rebase -i HEAD~3

# Rebase onto main
git rebase -i main
```

### Cherry-picking

```bash
# Apply a specific commit to current branch
git cherry-pick <commit-hash>
```

### Finding Bugs with Bisect

```bash
# Start bisect
git bisect start
git bisect bad  # Current version is bad
git bisect good <commit-hash>  # Known good version

# After testing each version
git bisect good  # or git bisect bad

# End bisect
git bisect reset
```

### Cleaning Up

```bash
# Remove untracked files (dry run)
git clean -n

# Remove untracked files
git clean -f

# Remove untracked files and directories
git clean -fd
```

## Useful Command Combinations

```bash
# Add all and commit in one line
git add . && git cm "Your message"

# Pull and push
git pull && git push

# Create branch, checkout, and push
git cob feature-name && git push -u origin feature-name

# View what changed in last commit
git show HEAD

# Compare two branches
git df main..feature-name
```

## Git Configuration Tips

### Useful Global Settings

```bash
# Auto-correct typos
git config --global help.autocorrect 1

# Cache credentials (timeout in seconds)
git config --global credential.helper 'cache --timeout=3600'

# Set pull strategy
git config --global pull.rebase false  # Merge (default)
git config --global pull.rebase true   # Rebase

# Prune remote branches on fetch
git config --global fetch.prune true
```

### Creating Custom Aliases

```bash
# Combination alias for common workflow
git config --global alias.sync '!git pull && git push'

# Show contributors
git config --global alias.contributors "shortlog -sn"

# Delete merged branches
git config --global alias.cleanup "!git branch --merged | grep -v '\\*\\|main\\|master' | xargs -n 1 git branch -d"
```

## Quick Reference Cheat Sheet

| Command | Shortcut | Description |
|---------|----------|-------------|
| `git status` | `git st` | Check repository status |
| `git status -s` | `git s` | Short status |
| `git branch` | `git br` | List branches |
| `git commit -m` | `git cm` | Commit with message |
| `git checkout` | `git co` | Switch branches |
| `git checkout -b` | `git cob` | Create and switch branch |
| `git diff` | `git df` | View changes |
| `git log --oneline --graph` | `git lg` | Pretty log |

## Best Practices

1. **Commit often** with meaningful messages
2. **Pull before push** to avoid conflicts
3. **Use branches** for features and fixes
4. **Review changes** before committing (`git df`)
5. **Keep commits atomic** - one logical change per commit
6. **Write good commit messages** - clear and descriptive
7. **Don't commit sensitive data** - use `.gitignore`
8. **Use `.gitignore`** for build artifacts and dependencies

## Common Issues and Solutions

### Forgot to create a branch before making changes

```bash
git stash
git cob new-feature
git stash pop
```

### Need to change last commit message

```bash
git commit --amend -m "New message"
```

### Accidentally committed to wrong branch

```bash
# On wrong branch
git log  # Copy the commit hash
git reset --hard HEAD~1

# Switch to correct branch
git co correct-branch
git cherry-pick <commit-hash>
```

### Merge conflicts

```bash
# After git merge or git pull shows conflicts
# 1. Edit files to resolve conflicts
# 2. Mark as resolved
git add conflicted-file.txt
# 3. Complete the merge
git commit
```

---

**Pro Tip:** Use `git aliases` to see all your configured shortcuts at any time!
