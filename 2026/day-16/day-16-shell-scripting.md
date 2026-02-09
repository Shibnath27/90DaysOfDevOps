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

---

## ✅ Task 5: Combine It All (server_check.sh)

```bash
vim server_check.sh
```

```bash
#!/bin/bash

SERVICE="nginx"

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


