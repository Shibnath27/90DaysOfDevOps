# 📄 `day-23-notes.md`

---

# Day 23 – Git Branching & GitHub Notes

---

## 🔹 Task 1: Understanding Branches

### What is a branch in Git?

A branch is a movable pointer to a commit.
It allows parallel development without affecting other lines of work.

---

### Why use branches instead of committing everything to `main`?

* Prevent breaking stable code
* Enable feature isolation
* Support team collaboration
* Allow code review before merging

In production workflows, `main` should remain stable.

---

### What is HEAD in Git?

`HEAD` is a pointer to the current branch (or commit) you are working on.

If you switch branches, `HEAD` moves.

---

### What happens to files when switching branches?

Git updates your working directory to match the snapshot of the target branch.

Files may:

* Appear
* Disappear
* Change content

Based on commit history of that branch.

---

## 🔹 Task 2: Branching Commands Used

### List all branches

```bash
git branch
```

Include remote branches:

```bash
git branch -a
```

---

### Create a Branch

```bash
git branch feature-1
```

---

### Switch to a Branch

```bash
git checkout feature-1
```

Modern alternative:

```bash
git switch feature-1
```

---

### Create and Switch in One Command

```bash
git checkout -b feature-2
```

Modern version:

```bash
git switch -c feature-2
```

---

### Delete a Branch

```bash
git branch -d feature-2
```

Force delete:

```bash
git branch -D feature-2
```

---

### Commit on Feature Branch

```bash
git commit -m "Add updates in feature-1"
```

When switching back to main:

```bash
git switch main
```

The commit from feature-1 will not exist in main unless merged.

---

## 🔹 Task 3: Push to GitHub

### Add Remote

```bash
git remote add origin https://github.com/username/devops-git-practice.git
```

---

### Verify Remote

```bash
git remote -v
```

---

### Push Main Branch

```bash
git push -u origin main
```

---

### Push Feature Branch

```bash
git push -u origin feature-1
```

---

### What is `origin` vs `upstream`?

* `origin` → Default name for the remote repository you cloned from.
* `upstream` → Conventionally refers to the original repository (used when working with forks).

---

## 🔹 Task 4: Pull from GitHub

### Pull Changes

```bash
git pull origin main
```

---

### Difference Between `git fetch` and `git pull`

* `git fetch` → Downloads changes but does not merge.
* `git pull` → Fetch + merge in one command.

Professional tip: Prefer `fetch` before `pull` in production environments.

---

## 🔹 Task 5: Clone vs Fork

### Clone

```bash
git clone https://github.com/user/repo.git
```

Creates a local copy of a repository.

---

### Fork

A GitHub feature that creates a copy of a repository under your GitHub account.

---

### Difference Between Clone and Fork

| Clone                | Fork                                      |
| -------------------- | ----------------------------------------- |
| Git operation        | GitHub platform operation                 |
| Copies repo locally  | Copies repo to your GitHub account        |
| Used for development | Used for contributing to others' projects |

---

### When to Use

* Clone → When you have direct access.
* Fork → When contributing to someone else’s project.

---

### Keep Fork in Sync

Add upstream remote:

```bash
git remote add upstream https://github.com/original/repo.git
```

Fetch upstream changes:

```bash
git fetch upstream
```

Merge into main:

```bash
git merge upstream/main
```

---

# 📄 Add These to `git-commands.md`

Add new category:

---

## 🔹 Branching

```bash
git branch
git branch feature-1
git switch feature-1
git switch -c feature-2
git branch -d feature-1
```

---

## 🔹 Remote & GitHub

```bash
git remote add origin <url>
git remote -v
git push -u origin main
git push -u origin feature-1
git fetch
git pull
```

