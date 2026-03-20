## Setup (One-time)

```bash
git config --global user.name "Your Name"
git config --global user.email "youremail@example.com"
```

---

## Starting a Project

```bash
git init                        # Initialize Git in current folder
git clone <repo-url>            # Clone a remote repo
```

---

## Working with Files

```bash
git status                      # Show changes
git add <file>                  # Stage a file
git add .                       # Stage all changes
git reset <file>                # Unstage a file
git diff                        # Show unstaged changes
git diff --staged               # Show staged changes
```

---

## Commit Changes

```bash
git commit -m "Message"         # Commit staged changes
git commit -am "Message"        # Stage + commit tracked files
```

---

## Branching

```bash
git branch                      # List branches
git branch <name>               # Create branch
git checkout <name>             # Switch branch
git checkout -b <name>          # Create & switch
git merge <branch>              # Merge into current branch
```

---

## Remote Repositories

```bash
git remote -v                   # Show remotes
git remote add origin <url>     # Add remote repo
git push -u origin main         # Push main branch first time
git push                        # Push changes
git pull                        # Fetch + merge changes
git fetch                       # Fetch without merge
```

---

## Undoing Mistakes

```bash
git restore <file>              # Discard changes
git reset --soft HEAD~1         # Undo last commit, keep changes
git reset --hard HEAD~1         # Undo last commit, discard changes
git revert <commit-id>          # Revert a commit safely
```

---

## Logs & History

```bash
git log                         # Full commit history
git log --oneline --graph       # Compact branch history
git show <commit-id>            # Show details of a commit
```

---

## Stash (Save work temporarily)

```bash
git stash                       # Save uncommitted changes
git stash list                  # Show stashes
git stash apply                 # Reapply last stash
git stash drop                  # Remove last stash
```

---

## Tags (Versions)

```bash
git tag v1.0                    # Create tag
git tag                         # List tags
git push origin v1.0            # Push tag to GitHub
```

---

**Quick Workflow Example**

```bash
git pull origin main            # Update project
git add .                       # Stage all changes
git commit -m "Feature update"  # Commit changes
git push origin main            # Push to GitHub
```

---
**Access token**

```
ghp_<YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
```
