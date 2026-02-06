# ✅ Day 11 – File Ownership Challenge (chown & chgrp)

> ⚠️ Most commands require `sudo`

---

## 🔹 Task 1: Understanding Ownership

Run in your home directory:

```bash
ls -l
```

 output:

<img width="502" height="144" alt="image" src="https://github.com/user-attachments/assets/767d3c2a-a5e0-4818-9197-447e116168a5" />

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

<img width="542" height="75" alt="image" src="https://github.com/user-attachments/assets/5e2c136c-a24b-4648-9b45-23cbd54d6949" />


### Change owner to `tokyo`

```bash
sudo chown tokyo devops-file.txt
ls -l devops-file.txt
```
<img width="550" height="68" alt="image" src="https://github.com/user-attachments/assets/96b3970d-ca16-4fe9-8e5e-ef75e41b96dc" />

### Change owner to `berlin`

```bash
sudo chown berlin devops-file.txt
ls -l devops-file.txt
```

<img width="565" height="66" alt="image" src="https://github.com/user-attachments/assets/f7ab99ff-c563-44e3-9fe0-ca75c6b3f575" />

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
<img width="547" height="61" alt="image" src="https://github.com/user-attachments/assets/dd6ec826-0190-4c75-9c8f-081e07b1d6e6" />

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

<img width="648" height="68" alt="image" src="https://github.com/user-attachments/assets/d53274ae-622d-4ad2-8115-308a5db3c0ee" />

---

### Directory example

```bash
mkdir app-logs
sudo chown berlin:heist-team app-logs
ls -ld app-logs
```

<img width="536" height="63" alt="image" src="https://github.com/user-attachments/assets/1fa5ca6d-e6e3-4c0a-a8e3-abfc8414623b" />

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

<img width="617" height="227" alt="image" src="https://github.com/user-attachments/assets/4d6267c2-b092-4a4d-b0fa-308917390d1a" />


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

<img width="639" height="95" alt="image" src="https://github.com/user-attachments/assets/8b818fdc-78b5-45c3-b7d9-da6c1ac73eb8" />

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


