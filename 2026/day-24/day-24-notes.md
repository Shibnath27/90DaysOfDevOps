# 📄 `day-24-notes.md`

---

# Day 24 – Advanced Git (Merge, Rebase, Stash, Cherry-Pick)

---

# 🔹 Task 1: Git Merge

## Fast-Forward Merge

A fast-forward merge happens when:

* `main` has not moved ahead
* Git simply moves the branch pointer forward

No merge commit is created.

Example:

```bash
git merge feature-login
```

History remains linear.

---

## Merge Commit

A merge commit happens when:

* Both branches have new commits
* Git must combine histories

Git creates a special merge commit with two parents.

---

## What is a Merge Conflict?

A merge conflict occurs when:

* The same line in the same file is modified in both branches.

Git cannot decide which version to keep.

You must manually resolve conflict markers:

```bash
<<<<<<< HEAD
main version
=======
feature version
>>>>>>> feature-branch
```

Then:

```bash
git add file
git commit
```

---

## Summary

| Scenario               | Result         |
| ---------------------- | -------------- |
| No new commits on main | Fast-forward   |
| Both branches diverged | Merge commit   |
| Same line edited       | Merge conflict |

---

# 🔹 Task 2: Git Rebase

## What Rebase Does

Rebase:

* Takes your commits
* Replays them on top of another branch
* Rewrites commit history

Example:

```bash
git rebase main
```

---

## History Comparison

Merge:

```
--A---B---C main
      \
       D---E feature
```

Rebase:

```
--A---B---C---D'---E'
```

Rebase creates a linear history.

---

## Why Not Rebase Shared Commits?

Rebase rewrites commit hashes.

If commits were pushed:

* History divergence occurs
* Teammates experience conflicts

Rule:
👉 Never rebase public/shared branches.

---

## When to Use

Use **rebase**:

* For cleaning local feature branch history
* Before merging into main

Use **merge**:

* For preserving branch history
* In shared collaborative branches

---

# 🔹 Task 3: Squash Merge

## Squash Merge

```bash
git merge --squash feature-profile
git commit -m "Add profile feature"
```

Combines all commits into one.

Main gets only 1 commit.

---

## Regular Merge

```bash
git merge feature-settings
```

All commits are preserved.

---

## Trade-Off

| Squash                         | Regular Merge              |
| ------------------------------ | -------------------------- |
| Clean history                  | Full detailed history      |
| Easier to read                 | More granular traceability |
| Loses individual commit detail | Keeps development steps    |

Use squash for:

* Small feature branches
* Cleanup commits (typos, formatting)

Use regular merge for:

* Complex features
* Auditable history

---

# 🔹 Task 4: Git Stash

## What is Stash?

Temporarily saves uncommitted changes.

---

### Save Work

```bash
git stash
```

With message:

```bash
git stash push -m "WIP dashboard"
```

---

### List Stashes

```bash
git stash list
```

---

### Apply Latest Stash

```bash
git stash pop
```

---

### Apply Specific Stash

```bash
git stash apply stash@{1}
```

---

## Difference Between Pop and Apply

| Command | Behavior             |
| ------- | -------------------- |
| `pop`   | Apply + remove stash |
| `apply` | Apply but keep stash |

---

## Real-World Use

* Urgent hotfix interrupts feature work
* Context switching
* Temporary experiments

---

# 🔹 Task 5: Cherry-Pick

## What Cherry-Pick Does

Applies a specific commit from one branch to another.

Example:

```bash
git cherry-pick <commit-hash>
```

Only that commit is copied.

---

## When to Use

* Production hotfix from feature branch
* Selective bug fix
* Backporting fixes

---

## Risks

* Duplicate commits
* History fragmentation
* Potential conflicts

Cherry-pick should be intentional, not routine.

---

# 🔥 Visualize Everything

```bash
git log --oneline --graph --all
```

This is your X-ray tool.

---

# 📄 Add to `git-commands.md`

---

## 🔹 Merge & Rebase

```bash
git merge branch-name
git merge --squash branch-name
git rebase main
```

---

## 🔹 Stash

```bash
git stash
git stash push -m "message"
git stash list
git stash pop
git stash apply stash@{n}
```

---

## 🔹 Cherry Pick

```bash
git cherry-pick <hash>
```

