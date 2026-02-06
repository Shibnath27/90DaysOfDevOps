# ✅ Day 10 – File Permissions & File Operations Challenge

> Run commands as your normal user unless stated.

---

## 🔹 Task 1: Create Files

### Create empty file

```bash
touch devops.txt
```

### Create notes.txt with content

```bash
echo "This is my DevOps notes file" > notes.txt
```

### Create script.sh using vim

```bash
vim script.sh
```

Inside `vim`, press `i` and add:

```bash
echo "Hello DevOps"
```

Save & exit:

```
ESC :wq
```

### Verify permissions

```bash
ls -l devops.txt notes.txt script.sh
```

---

## 🔹 Task 2: Read Files

```bash
cat notes.txt
```

### Open script in read-only mode

```bash
vim -R script.sh
```

### View /etc/passwd

```bash
head -n 5 /etc/passwd
tail -n 5 /etc/passwd
```

---

## 🔹 Task 3: Understand Permissions

Check permissions:

```bash
ls -l devops.txt notes.txt script.sh
```

Example output:

```text
-rw-r--r--  devops.txt
-rw-r--r--  notes.txt
-rw-r--r--  script.sh
```

Meaning:

* **Owner**: read + write
* **Group**: read
* **Others**: read
* ❌ No execute permission yet

---

## 🔹 Task 4: Modify Permissions

### Make script executable

```bash
chmod +x script.sh
./script.sh
```

Expected output:

```text
Hello DevOps
```

---

### Make devops.txt read-only

```bash
chmod a-w devops.txt
```

Verify:

```bash
ls -l devops.txt
```

---

### Set notes.txt to 640

```bash
chmod 640 notes.txt
```

Meaning:

* Owner: rw
* Group: r
* Others: none

---

### Create directory with 755

```bash
mkdir project
chmod 755 project
ls -ld project
```

---

## 🔹 Task 5: Test Permissions

### Write to read-only file

```bash
echo "test" >> devops.txt
```

Expected:

```text
Permission denied
```

---

### Remove execute permission and test

```bash
chmod -x script.sh
./script.sh
```

Expected:

```text
Permission denied
```

---

# 📄 `day-10-file-permissions.md` (FINAL)

```markdown
# Day 10 – File Permissions & File Operations Challenge

## Files Created
- devops.txt
- notes.txt
- script.sh
- project/ (directory)

## Permission Changes
- script.sh: made executable using chmod +x
- devops.txt: made read-only by removing write permission
- notes.txt: permissions set to 640
- project/: directory permissions set to 755

## Commands Used
- touch
- echo >
- vim
- cat
- head, tail
- chmod
- ls -l
- mkdir

## What I Learned
- How Linux permissions control file access and execution
- Why execute permission is required for scripts
- How incorrect permissions lead to real production issues
```

---
t, RDX** 💪
