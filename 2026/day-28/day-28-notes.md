# 📄 `day-28-notes.md`

---

# Day 28 – Full Revision (Day 1–27)

---

# 🔹 Task 1: Self-Assessment Checklist

## Linux

* ✅ Navigate filesystem, manage files/directories
* ✅ Manage processes (`ps`, `top`, `kill`, `bg`, `fg`)
* ✅ Work with systemd (`systemctl start/stop/status/enable`)
* ✅ Edit text files (vim/nano)
* ✅ Troubleshoot CPU/memory/disk (`top`, `free`, `df`, `du`)
* ✅ Explain filesystem hierarchy (`/etc`, `/var`, `/home`, etc.)
* ✅ Manage users & groups
* ✅ File permissions (`chmod` symbolic & numeric)
* ✅ Ownership (`chown`, `chgrp`)
* ⚠️ LVM (Need deeper repetition)
* ✅ Network troubleshooting (`ping`, `curl`, `ss`, `dig`)
* ⚠️ Subnetting calculations (Need more speed/accuracy)

---

## Shell Scripting

* ✅ Variables, arguments, user input
* ✅ Conditionals (`if`, `case`)
* ✅ Loops (`for`, `while`, `until`)
* ⚠️ Functions with return codes (Need refinement)
* ⚠️ Advanced text processing (`awk`, `sed`)
* ✅ Error handling (`set -euo pipefail`, `trap`)
* ✅ Crontab scheduling

---

## Git & GitHub

* ✅ Init, add, commit, log
* ✅ Branching & switching
* ✅ Push/pull
* ✅ Clone vs fork
* ✅ Merge & fast-forward
* ✅ Rebase (theory + practical)
* ✅ Stash
* ✅ Cherry-pick
* ✅ Squash vs regular merge
* ✅ Reset vs revert
* ✅ Branching strategies
* ✅ GitHub CLI basics

---

# 🔹 Task 2: Weak Spots Revisited

### 1️⃣ LVM

Re-practiced:

* `pvcreate`
* `vgcreate`
* `lvcreate`
* `lvextend`
* `resize2fs`

Re-learned:

* LVM adds abstraction layer (PV → VG → LV)
* Allows dynamic resizing without repartitioning
* Critical in production storage scaling

---

### 2️⃣ Subnetting Speed

Revisited:

* CIDR notation
* /24 vs /16 vs /28
* Calculating host ranges

Key clarity:

* Subnet mask determines network vs host bits
* Smaller CIDR = larger network

---

### 3️⃣ awk & sed

Re-practiced:

```bash
awk '{print $1}'
sed 's/error/warning/g'
```

Realization:

* `awk` = column-based processor
* `sed` = stream editor

Powerful for log analysis automation.

---

# 🔹 Task 3: Quick-Fire Answers

### What does `chmod 755 script.sh` do?

Owner: read, write, execute
Group: read, execute
Others: read, execute

Numeric breakdown:
7 = rwx
5 = r-x

---

### Process vs Service

Process: Running instance of a program.
Service: Managed background process controlled by systemd.

All services are processes, not all processes are services.

---

### Find process using port 8080

```bash
ss -tulpn | grep 8080
```

or

```bash
lsof -i :8080
```

---

### What does `set -euo pipefail` do?

* `-e` → Exit on error
* `-u` → Treat unset variables as errors
* `-o pipefail` → Fail if any command in pipeline fails

Makes scripts production-safe.

---

### `git reset --hard` vs `git revert`

`reset --hard`:

* Rewrites history
* Deletes changes

`revert`:

* Creates new commit undoing changes
* Safe for shared branches

---

### Branching strategy for 5 devs shipping weekly?

GitHub Flow.

Simple, PR-based, supports fast iteration.

---

### What does `git stash` do?

Temporarily saves uncommitted changes so you can switch branches safely.

Used for context switching.

---

### Schedule script daily at 3 AM

```bash
0 3 * * * /path/script.sh
```

---

### `git fetch` vs `git pull`

`fetch` → Downloads changes only
`pull` → Fetch + merge

---

### What is LVM?

Logical Volume Manager.

Adds flexible storage management:

* Resize volumes
* Combine disks
* Snapshot capability

Better than static partitions in dynamic environments.

---

# 🔹 Task 4: Organization Check

* All Day 1–27 commits pushed
* `git-commands.md` updated
* Shell cheat sheet complete
* Profile cleaned (Day 27 standards maintained)

---

# 🔹 Task 5: Teach It Back

### Explaining Git Branching to a Non-Developer

Think of Git like a notebook.
The “main” branch is your official notebook.

When you want to try something new, you make a photocopy (branch).
You experiment on the copy without damaging the original.

If it works, you merge it back.
If it fails, you throw away the copy.

Branching allows safe experimentation.

---

# 🔥 Honest Reflection

Strengths:

* Git fundamentals solid
* Linux command-line comfort improved
* Shell scripting automation capability developing

Improvement Areas:

* Faster subnet calculations
* Advanced `awk/sed`
* More real-world troubleshooting scenarios

