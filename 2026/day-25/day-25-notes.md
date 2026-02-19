# 📄 `day-25-notes.md`

---

# Day 25 – Git Reset, Revert & Branching Strategies

---

# 🔹 Task 1: Git Reset — Hands-On

Assume commit history:

```
A → B → C (HEAD)
```

---

## `git reset --soft HEAD~1`

```bash
git reset --soft HEAD~1
```

**What happens:**

* Moves HEAD back to commit B
* Changes from commit C remain staged

You can recommit immediately.

---

## `git reset --mixed HEAD~1` (default)

```bash
git reset --mixed HEAD~1
```

**What happens:**

* Moves HEAD back
* Changes become unstaged
* Files remain modified in working directory

---

## `git reset --hard HEAD~1`

```bash
git reset --hard HEAD~1
```

**What happens:**

* Moves HEAD back
* Removes staged changes
* Deletes working directory changes

⚠️ This is destructive.

---

## Differences

| Mode      | HEAD Moves | Staging   | Working Directory |
| --------- | ---------- | --------- | ----------------- |
| `--soft`  | Yes        | Preserved | Preserved         |
| `--mixed` | Yes        | Cleared   | Preserved         |
| `--hard`  | Yes        | Cleared   | Deleted           |

---

## Which Is Destructive?

`--hard` is destructive because it permanently deletes uncommitted changes.

Recovery only possible via:

```bash
git reflog
```

---

## When to Use

* `--soft` → Rewrite recent commit message or combine commits
* `--mixed` → Unstage changes
* `--hard` → Discard local work completely

---

## Should You Reset Pushed Commits?

❌ No.

Reset rewrites history.
If already pushed, it causes divergence and breaks collaborators’ history.

---

# 🔹 Task 2: Git Revert

Assume:

```
X → Y → Z (HEAD)
```

Revert commit Y:

```bash
git revert <hash-of-Y>
```

---

## What Happens?

* Git creates a new commit
* That commit reverses the changes introduced by Y
* Commit Y still exists in history

History becomes:

```
X → Y → Z → Revert-Y
```

---

## Key Differences

* `reset` rewrites history
* `revert` creates a new corrective commit

---

## Why Revert Is Safer

* Does not alter commit history
* Safe for shared branches
* Preserves audit trail

---

## When to Use

* Production bug fix
* Shared branch correction
* Rolling back a specific commit safely

---

# 🔹 Task 3: Reset vs Revert Comparison

|                              | git reset                     | git revert                           |
| ---------------------------- | ----------------------------- | ------------------------------------ |
| What it does                 | Moves branch pointer backward | Creates new commit reversing changes |
| Removes commit from history? | Yes                           | No                                   |
| Safe for shared branches?    | No                            | Yes                                  |
| When to use                  | Local history cleanup         | Undo pushed/shared commits           |

---

# 🔹 Task 4: Branching Strategies

---

## 1️⃣ GitFlow

### How It Works

Multiple long-lived branches:

* `main`
* `develop`
* `feature/*`
* `release/*`
* `hotfix/*`

### Flow

```
main
  ↑
release
  ↑
develop ← feature branches
```

### Used In

* Enterprise environments
* Scheduled releases

### Pros

* Structured
* Release control
* Parallel work streams

### Cons

* Complex
* Heavy process
* Slower iteration

---

## 2️⃣ GitHub Flow

### How It Works

* Single `main` branch
* Short-lived feature branches
* Pull request → review → merge

### Flow

```
main ← feature-branch → PR → merge
```

### Used In

* SaaS companies
* Continuous deployment

### Pros

* Simple
* Fast
* Easy CI/CD integration

### Cons

* Requires strong testing discipline

---

## 3️⃣ Trunk-Based Development

### How It Works

* Developers commit frequently to `main`
* Very short-lived branches (or none)

### Flow

```
main ← small frequent commits
```

### Used In

* High-performing DevOps teams
* Google-style engineering

### Pros

* Minimal merge conflicts
* Faster integration
* Encourages CI automation

### Cons

* Requires mature testing pipelines
* Not forgiving without automation

---

# 🔹 Strategy Selection

### Startup Shipping Fast?

✅ GitHub Flow
Simple, fast iteration, CI-driven.

---

### Large Team with Scheduled Releases?

✅ GitFlow
Structured release management.

---

### Open Source Example

* Kubernetes uses a structured branching model with release branches.
* React primarily follows a simplified GitHub Flow style.
* Linux kernel follows a hierarchical integration model similar to trunk-based with maintainers.

---

# 🔹 Update `git-commands.md`

Add:

---

## Reset & Revert

```bash
git reset --soft HEAD~1
git reset --mixed HEAD~1
git reset --hard HEAD~1
git revert <commit-hash>
git reflog
```


