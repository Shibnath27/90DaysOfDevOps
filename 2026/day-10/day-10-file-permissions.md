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
<img width="920" height="86" alt="image" src="https://github.com/user-attachments/assets/30b87a27-42a4-44ed-a684-e28e9275a297" />

---

## 🔹 Task 2: Read Files

```bash
cat notes.txt
```
<img width="419" height="55" alt="image" src="https://github.com/user-attachments/assets/92bfd2a1-c255-4609-a3ad-3552bb99efd6" />


### Open script in read-only mode

```bash
vim -R script.sh
```
<img width="1143" height="610" alt="image" src="https://github.com/user-attachments/assets/65bcf325-39ae-495e-99de-48f3dbca9a72" />


### View /etc/passwd

```bash
head -n 5 /etc/passwd
tail -n 5 /etc/passwd
```
<img width="592" height="214" alt="image" src="https://github.com/user-attachments/assets/e1bc6013-c009-4143-9ec6-c194e7c39e9d" />

---

## 🔹 Task 3: Understand Permissions

Check permissions:

```bash
ls -l devops.txt notes.txt script.sh
```

 output:

<img width="542" height="86" alt="image" src="https://github.com/user-attachments/assets/e14f2cc8-5d32-43c9-ad60-d39dee33c809" />

```
```
Meaning:

* **Owner**: read + write
* **Group**: read + write
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
<img width="502" height="66" alt="image" src="https://github.com/user-attachments/assets/fc41de69-cd52-4985-87b5-db99142a6d93" />

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
<img width="533" height="54" alt="image" src="https://github.com/user-attachments/assets/f16ccaae-acad-4749-b1e2-5b632e74fa33" />

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

