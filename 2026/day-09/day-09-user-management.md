# ✅ Day 09 – Linux User & Group Management (Hands-on Guide)

> ⚠️ Run all commands as **root** or with `sudo`

---

## 🔹 Task 1: Create Users

### Create users with home directories

```bash
sudo useradd -m tokyo
sudo useradd -m berlin
sudo useradd -m professor
```
<img width="604" height="77" alt="image" src="https://github.com/user-attachments/assets/78009370-fde5-4051-aeeb-ee07a6e93e3f" />

### Set passwords

```bash
sudo passwd tokyo
sudo passwd berlin
sudo passwd professor
```

### Verify

```bash
cat /etc/passwd | grep -E "tokyo|berlin|professor"
ls -l /home/
```
<img width="629" height="353" alt="image" src="https://github.com/user-attachments/assets/c63f835e-6bbf-4f77-ae73-8bfbd881b014" />

---

## 🔹 Task 2: Create Groups

```bash
sudo groupadd developers
sudo groupadd admins
```

### Verify

```bash
cat /etc/group | grep -E "developers|admins"
```
<img width="598" height="69" alt="image" src="https://github.com/user-attachments/assets/d4cc054e-493d-4c2e-9efb-ae5f27cedad2" />

---

## 🔹 Task 3: Assign Users to Groups

```bash
sudo usermod -aG developers tokyo
sudo usermod -aG developers,admins berlin
sudo usermod -aG admins professor
```

### Verify group membership

```bash
groups tokyo
groups berlin
groups professor
```
<img width="564" height="107" alt="image" src="https://github.com/user-attachments/assets/225dae91-5550-4672-be64-dc49bc139afb" />

---

## 🔹 Task 4: Shared Directory (/opt/dev-project)

### Create directory

```bash
sudo mkdir /opt/dev-project
```

### Set group owner

```bash
sudo chgrp developers /opt/dev-project
```

### Set permissions (775)

```bash
sudo chmod 775 /opt/dev-project
```

### Verify

```bash
ls -ld /opt/dev-project
```
<img width="524" height="48" alt="image" src="https://github.com/user-attachments/assets/35db76c7-38b5-4380-955b-e99e0ced5149" />


### Test as users

```bash
sudo -u tokyo touch /opt/dev-project/tokyo.txt
sudo -u berlin touch /opt/dev-project/berlin.txt
ls -l /opt/dev-project
```
<img width="629" height="111" alt="image" src="https://github.com/user-attachments/assets/d6935555-2ae7-4961-ae81-3da0929bad1f" />

✅ Both users should be able to create files.

---

## 🔹 Task 5: Team Workspace

### Create user & group

```bash
sudo useradd -m nairobi
sudo passwd nairobi
sudo groupadd project-team
```

### Add users to group

```bash
sudo usermod -aG project-team nairobi
sudo usermod -aG project-team tokyo
```

### Create workspace

```bash
sudo mkdir /opt/team-workspace
sudo chgrp project-team /opt/team-workspace
sudo chmod 775 /opt/team-workspace
```

### Test

```bash
sudo -u nairobi touch /opt/team-workspace/nairobi.txt
ls -l /opt/team-workspace
```
<img width="750" height="80" alt="image" src="https://github.com/user-attachments/assets/5d96bc38-5281-4026-9721-55ae50ee8055" />

---

# 📄 `day-09-user-management.md` (Submission-Ready)

```markdown
# Day 09 – Linux User & Group Management Challenge

## Users & Groups Created
- Users: tokyo, berlin, professor, nairobi
- Groups: developers, admins, project-team

## Group Assignments
- tokyo → developers, project-team
- berlin → developers, admins
- professor → admins
- nairobi → project-team

## Directories Created
- /opt/dev-project (group: developers, permissions: 775)
- /opt/team-workspace (group: project-team, permissions: 775)

## Commands Used
- useradd, passwd
- groupadd
- usermod -aG
- mkdir
- chgrp
- chmod
- groups
- sudo -u

## What I Learned
- How Linux users and groups control access
- How shared directories work using group permissions
- How to test permissions using sudo -u
```

---

