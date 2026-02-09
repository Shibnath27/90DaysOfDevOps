## Day 16 – Shell Scripting Basics ✅

Switch to a working directory first:

```bash
cd ~
mkdir day-16-shell
cd day-16-shell
```

---

## ✅ Task 1: Your First Script (hello.sh)

### Create script

```bash
vim hello.sh
```

### Content

```bash
#!/bin/bash
echo "Hello, DevOps!"
```
<img width="595" height="80" alt="image" src="https://github.com/user-attachments/assets/ddfdb8ca-c8cf-4f4e-ab4a-733c47ac9ce1" />

### Make executable & run

```bash
chmod +x hello.sh
./hello.sh
```

### ❓ What if you remove the shebang?

* Script **may still run** if you execute it like:

  ```bash
  bash hello.sh
  ```
* But `./hello.sh` will fail or behave unpredictably
  ✅ **Shebang tells Linux which interpreter to use**

---

## ✅ Task 2: Variables (variables.sh)

```bash
vim variables.sh
```

```bash
#!/bin/bash

NAME="Shibnath"
ROLE="DevOps Engineer"

echo "Hello, I am $NAME and I am a $ROLE"
```

Run:

```bash
chmod +x variables.sh
./variables.sh
```
<img width="551" height="62" alt="image" src="https://github.com/user-attachments/assets/79730dc8-7f6f-4bd3-9037-a81e9fbdb501" />

### 🔍 Single vs Double Quotes

```bash
echo 'Hello $NAME'   # ❌ No variable expansion
echo "Hello $NAME"   # ✅ Variable expands
```

---

## ✅ Task 3: User Input with read (greet.sh)

```bash
vim greet.sh
```

```bash
#!/bin/bash

read -p "Enter your name: " NAME
read -p "Enter your favourite tool: " TOOL

echo "Hello $NAME, your favourite tool is $TOOL"
```

Run:

```bash
chmod +x greet.sh
./greet.sh
```

<img width="476" height="121" alt="image" src="https://github.com/user-attachments/assets/b4af0207-414a-4a9a-8f76-ed5f54ead5b3" />

---

## ✅ Task 4: If–Else Conditions

### 🔹 check_number.sh

```bash
vim check_number.sh
```

```bash
#!/bin/bash

read -p "Enter a number: " NUM

if [ "$NUM" -gt 0 ]; then
  echo "Number is positive"
elif [ "$NUM" -lt 0 ]; then
  echo "Number is negative"
else
  echo "Number is zero"
fi
```
<img width="505" height="101" alt="image" src="https://github.com/user-attachments/assets/c0193cd7-310a-47d3-a5f4-3eec82deac92" />

---

### 🔹 file_check.sh

```bash
vim file_check.sh
```

```bash
#!/bin/bash

read -p "Enter filename: " FILE

if [ -f "$FILE" ]; then
  echo "File exists"
else
  echo "File does not exist"
fi
```

<img width="474" height="76" alt="image" src="https://github.com/user-attachments/assets/0f86a99f-40be-43ae-b0f3-f9e5cb8ffcb3" />

---

## ✅ Task 5: Combine It All (server_check.sh)

```bash
vim server_check.sh
```

```bash
#!/bin/bash

read -p "Enter a service name : " SERVICE
read -p "Do you want to check the status of $SERVICE? (y/n): " ANSWER

if [ "$ANSWER" = "y" ]; then
  systemctl is-active --quiet $SERVICE
  if [ $? -eq 0 ]; then
    echo "$SERVICE is running"
  else
    echo "$SERVICE is NOT running"
  fi
else
  echo "Skipped."
fi
```

Run:

```bash
chmod +x server_check.sh
./server_check.sh
```
<img width="529" height="85" alt="image" src="https://github.com/user-attachments/assets/1dbf0679-3973-4dc7-80c8-6b293b833bd3" />


📌 **Real DevOps pattern:** checking exit codes with `$?`

---

## 📄 What to write in `day-16-shell-scripting.md`

```md
# Day 16 – Shell Scripting Basics

## Scripts Created
- hello.sh
- variables.sh
- greet.sh
- check_number.sh
- file_check.sh
- server_check.sh

## Concepts Learned
- Shebang defines the interpreter for scripts
- Variables expand only in double quotes
- read enables interactive scripts
- if-else logic drives automation decisions
- Exit codes help validate command success

## Commands Used
chmod +x
./script.sh
read
if [ condition ]
systemctl is-active

## Key Takeaway
Shell scripting is about automating checks and decisions, not just writing commands.
```

---


