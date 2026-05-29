# Git Cheat Sheet — Only What You Use Daily

---

## Setup (one-time)

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

---

## Start a Project

```bash
git init                        # new repo
git clone <url>                 # copy existing repo
```

---

## The Loop (you'll do this 100x a day)

```bash
git status                      # what's going on?
git add .                       # stage everything
git add <file>                  # stage one file
git commit -m "message"         # save snapshot
git push                        # send to GitHub
git pull                        # get latest from GitHub
```

---

## Branches

```bash
git branch                      # list branches
git switch -c <name>            # create + switch
git switch <name>               # switch to branch
git merge <branch>              # merge into current
git branch -d <name>            # delete branch
```

---

## Undo Stuff

```bash
git restore <file>              # discard changes
git restore --staged <file>     # unstage
git reset HEAD~1                # undo last commit, keep changes
git revert <hash>               # undo a pushed commit safely
```

---

## Check History

```bash
git log --oneline               # short log
git diff                        # see unstaged changes
git diff --staged               # see staged changes
```

---

## Stash (save work temporarily)

```bash
git stash                       # save
git stash pop                   # restore
```

---

## Remote

```bash
git remote -v                   # show remotes
git push -u origin main         # first push
git push --force-with-lease     # safe force push
```

---

## Tags

```bash
git tag -a v1.0.0 -m "msg"     # tag a release
git push --tags                 # push tags
```

---

*That's it. This covers 95% of daily Git usage.*
