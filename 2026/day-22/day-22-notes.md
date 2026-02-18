# 📄 `git-commands.md`

---

# Git Commands Reference – DevOps Practice

---

## 🔹 Setup & Configuration

### Check Git Version

```bash
git --version
```

Verifies Git is installed.

---

### Configure Username

```bash
git config --global user.name "Shibnath Das"
```

Sets your commit author name.

---

### Configure Email

```bash
git config --global user.email "your@email.com"
```

Sets your commit email.

---

### Verify Configuration

```bash
git config --list
```

Displays current Git settings.

---

## 🔹 Repository Initialization

### Initialize Repository

```bash
git init
```

Creates a new Git repository in current directory.

---

### Check Repository Status

```bash
git status
```

Shows:

* Modified files
* Untracked files
* Staged files
* Current branch

---

## 🔹 Basic Workflow

### Stage File

```bash
git add git-commands.md
```

Moves file to staging area.

Stage everything:

```bash
git add .
```

---

### Check What’s Staged

```bash
git status
```

---

### Commit Changes

```bash
git commit -m "Add initial git commands reference"
```

Saves staged changes to repository history.

---

## 🔹 Viewing Changes

### View Commit History

```bash
git log
```

Compact format:

```bash
git log --oneline
```

Graph view:

```bash
git log --oneline --graph --decorate
```

---

### See What Changed (Before Staging)

```bash
git diff
```

---

### See What’s Staged

```bash
git diff --staged
```

---

### View File History

```bash
git log -- git-commands.md
```

---

# 📄 `day-22-notes.md`

---

# Day 22 – Git Notes

---

## 1️⃣ Difference Between `git add` and `git commit`

* `git add` moves changes to the **staging area**.
* `git commit` permanently saves staged changes to repository history.

You cannot commit changes unless they are staged.

---

## 2️⃣ What Does the Staging Area Do?

The staging area acts as a **preview layer** between working directory and repository.

It allows:

* Partial commits
* Cleaner commit grouping
* Logical change organization

Git does not commit directly so developers can control exactly what enters history.

---

## 3️⃣ What Information Does `git log` Show?

`git log` shows:

* Commit hash (SHA)
* Author
* Date
* Commit message

This forms the permanent history of the project.

---

## 4️⃣ What Is the `.git/` Folder?

The `.git/` folder contains:

* Commit history
* Branch references
* Configuration
* Object database

If you delete `.git/`, the project stops being a Git repository.
All version history is permanently lost.

---

## 5️⃣ Working Directory vs Staging Area vs Repository

**Working Directory**
Where you edit files.

**Staging Area (Index)**
Where you prepare changes for commit.

**Repository (.git folder)**
Where committed history is stored permanently.

Flow:

```
Working Directory → (git add) → Staging Area → (git commit) → Repository
```

---

# 🔥 Recommended Commit Sequence (Clean History)

Example progression:

```bash
git commit -m "Initialize repository and add git-commands reference"
git commit -m "Add viewing and diff commands"
git commit -m "Add git log compact formats"
git commit -m "Add Git workflow notes"
```

View clean history:

```bash
git log --oneline
```

