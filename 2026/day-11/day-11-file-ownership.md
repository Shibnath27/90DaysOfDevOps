# ✅ Day 11 – File Ownership Challenge (chown & chgrp)

> ⚠️ Most commands require `sudo`

---

## 🔹 Task 1: Understanding Ownership

Run in your home directory:

```bash
ls -l
```

Example output:

```text
-rw-r--r-- 1 ubuntu ubuntu 414 Feb 5  devops.txt
```

### Breakdown:

* **Owner** → `ubuntu`
* **Group** → `ubuntu`
* Owner controls file as primary user
* Group allows **shared access** among multiple users

📌 **Difference**

* **Owner**: primary controller of the file
* **Group**: enables collaboration without changing owner

---

## 🔹 Task 2: Basic `chown` Operations

### Create file

```bash
touch devops-file.txt
ls -l devops-file.txt
```

### Change owner to `tokyo`

```bash
sudo chown tokyo devops-file.txt
ls -l devops-file.txt
```

### Change owner to `berlin`

```bash
sudo chown berlin devops-file.txt
ls -l devops-file.txt
```

---

## 🔹 Task 3: Basic `chgrp` Operations

### Create file

```bash
touch team-notes.txt
ls -l team-notes.txt
```

### Create group

```bash
sudo groupadd heist-team
```

### Change group

```bash
sudo chgrp heist-team team-notes.txt
ls -l team-notes.txt
```

---

## 🔹 Task 4: Change Owner & Group Together

### Create file

```bash
touch project-config.yaml
```

### Change owner + group (single command)

```bash
sudo chown professor:heist-team project-config.yaml
ls -l project-config.yaml
```

---

### Directory example

```bash
mkdir app-logs
sudo chown berlin:heist-team app-logs
ls -ld app-logs
```

---

## 🔹 Task 5: Recursive Ownership (`-R`)

### Create directory structure

```bash
mkdir -p heist-project/vault
mkdir -p heist-project/plans
touch heist-project/vault/gold.txt
touch heist-project/plans/strategy.conf
```

### Create group

```bash
sudo groupadd planners
```

### Recursive ownership change

```bash
sudo chown -R professor:planners heist-project/
```

### Verify

```bash
ls -lR heist-project/
```

✅ All files & directories should now show `professor planners`

---

## 🔹 Task 6: Practice Challenge (Realistic)

### Create users (if not exist)

```bash
sudo useradd -m tokyo
sudo useradd -m berlin
sudo useradd -m nairobi
```

### Create groups

```bash
sudo groupadd vault-team
sudo groupadd tech-team
```

### Create directory & files

```bash
mkdir bank-heist
touch bank-heist/access-codes.txt
touch bank-heist/blueprints.pdf
touch bank-heist/escape-plan.txt
```

### Set ownership

```bash
sudo chown tokyo:vault-team bank-heist/access-codes.txt
sudo chown berlin:tech-team bank-heist/blueprints.pdf
sudo chown nairobi:vault-team bank-heist/escape-plan.txt
```

### Verify

```bash
ls -l bank-heist/
```

---

# 📄 `day-11-file-ownership.md` (FINAL)

```markdown
# Day 11 – File Ownership Challenge (chown & chgrp)

## Files & Directories Created
- devops-file.txt
- team-notes.txt
- project-config.yaml
- app-logs/
- heist-project/
- bank-heist/

## Ownership Changes
- devops-file.txt: ubuntu → tokyo → berlin
- team-notes.txt: group changed to heist-team
- project-config.yaml: professor:heist-team
- app-logs/: berlin:heist-team
- heist-project/: professor:planners (recursive)
- bank-heist/access-codes.txt: tokyo:vault-team
- bank-heist/blueprints.pdf: berlin:tech-team
- bank-heist/escape-plan.txt: nairobi:vault-team

## Commands Used
- ls -l, ls -lR
- chown
- chgrp
- groupadd
- useradd
- mkdir
- touch

## What I Learned
- Difference between file owner and group
- How to change ownership safely using chown and chgrp
- How recursive ownership is critical for real projects
```

---


