# 📄 `day-27-notes.md`

---

# Day 27 – GitHub Profile Makeover

---

# 🔹 Task 1: Profile Audit (Before Changes)

## First Impression Review

When viewing my profile as a recruiter:

* Profile picture: [Professional / Needs improvement]
* Bio: [Was it clear what I do?]
* Pinned repos: [Relevant or random?]
* Repo descriptions: [Filled or blank?]
* README quality: [Structured or missing?]

### Observations

* Was my DevOps journey visible immediately?
* Could someone understand my technical direction in 30 seconds?
* Did my repositories reflect consistency?

---

# 🔹 Task 2: Profile README Creation

Created special repository:

```
github.com/<your-username>/<your-username>
```

Added structured `README.md` with:

### Included Sections

* Short introduction (DevOps-focused identity)
* Current focus: 90 Days of DevOps
* Skills:

  * Linux
  * Git & GitHub
  * Shell scripting
  * Python
  * CI/CD (learning phase)
* Featured repositories
* LinkedIn contact link

### Design Principle Used

* Clean markdown
* Minimal badges
* No unnecessary animations
* Strong clarity > visual noise

---

# 🔹 Task 3: Repository Organization

---

## 1️⃣ 90 Days of DevOps

Repository structure:

```
/day-01
/day-02
...
/day-27
```

README includes:

* Challenge overview
* Tech stack
* Learning goals
* Daily structure

---

## 2️⃣ shell-scripts

Moved scripts from Days 16–21.

README includes:

* Script name
* Purpose
* Usage example

Example structure:

```
/disk-monitor.sh
/log-rotate.sh
/user-management.sh
```

Added `.gitignore` for:

```
*.log
.env
```

---

## 3️⃣ python-scripts

Moved scripts from Days 7–15.

README includes:

* Project list
* What each script solves
* Dependencies (if any)

---

## 4️⃣ devops-notes

Organized:

```
/linux
/git
/python
/cheatsheets
```

Added:

* `git-commands.md`
* Shell scripting cheat sheet
* Structured markdown files

---

# 🔹 Task 4: Pinned Repositories

Selected 6 repositories based on:

* Demonstrates growth
* Shows automation capability
* Reflects DevOps direction
* Has proper README and documentation

Removed random forks from pinned section.

---

# 🔹 Task 5: Clean-Up Actions

* Deleted empty test repositories
* Renamed unclear repos using hyphen-style naming
* Verified no secrets committed:

  * Checked `.env`
  * Searched commit history
* Ensured `.gitignore` present in all repos

Used:

```bash
git log
git grep "API_KEY"
```

---

# 🔹 Task 6: Before vs After

<img width="1365" height="613" alt="image" src="https://github.com/user-attachments/assets/794c3e2a-3b70-4192-91dc-9e573ca51e76" />

<img width="1351" height="609" alt="image" src="https://github.com/user-attachments/assets/bc8ee47c-a34d-465f-8f10-3258a2cf92d4" />

<img width="1356" height="603" alt="image" src="https://github.com/user-attachments/assets/470dedec-1754-4156-8512-2ab4b47902f3" />

### Improvements Made

1️⃣ Clear DevOps identity
Previously generic. Now aligned with DevOps learning path.

2️⃣ Structured repositories
Organized by purpose instead of random uploads.

3️⃣ Professional documentation
Every repo now explains what it does and why it exists.

---

# 🔥 Example Profile README Template (You Can Use)

```markdown
# Hi, I'm Shibnath 👋

DevOps Engineer | Automating Infrastructure | 90 Days of DevOps

## 🚀 Currently Working On
- 90 Days of DevOps Challenge
- Advanced Git & GitHub workflows
- Shell scripting automation

## 🛠️ Skills & Tools
- Linux
- Git & GitHub
- Bash / Shell
- Python
- CI/CD (Learning)

## 📂 Featured Projects
- 90-days-of-devops
- shell-scripts
- python-scripts
- devops-notes

## 📫 Connect With Me
- LinkedIn: <your-link>
```

---


