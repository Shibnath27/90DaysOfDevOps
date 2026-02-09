# Day 17 – Shell Scripting: Loops, Arguments & Error Handling ✅

Create a working folder:

```bash
cd ~
mkdir day-17-shell
cd day-17-shell
```

---

## ✅ Task 1: For Loops

### 🔹 for_loop.sh (loop through fruits)

```bash
vim for_loop.sh
```

```bash
#!/bin/bash

FRUITS=("apple" "banana" "orange" "mango" "grapes")

for fruit in "${FRUITS[@]}"; do
  echo "Fruit: $fruit"
done
```

Run:

```bash
chmod +x for_loop.sh
./for_loop.sh
```

---

### 🔹 count.sh (1 to 10)

```bash
vim count.sh
```

```bash
#!/bin/bash

for i in {1..10}; do
  echo "$i"
done
```

📌 **DevOps use:** iterating over servers, containers, files.

---

## ✅ Task 2: While Loop

### 🔹 countdown.sh

```bash
vim countdown.sh
```

```bash
#!/bin/bash

read -p "Enter a number: " NUM

while [ "$NUM" -ge 0 ]; do
  echo "$NUM"
  NUM=$((NUM - 1))
done

echo "Done!"
```

Run:

```bash
chmod +x countdown.sh
./countdown.sh
```

📌 **Real-world:** retries, wait-for-service loops.

---

## ✅ Task 3: Command-Line Arguments

### 🔹 greet.sh (arguments)

```bash
vim greet.sh
```

```bash
#!/bin/bash

if [ -z "$1" ]; then
  echo "Usage: ./greet.sh <name>"
  exit 1
fi

echo "Hello, $1!"
```

Test:

```bash
./greet.sh
./greet.sh Shibnath
```

---

### 🔹 args_demo.sh

```bash
vim args_demo.sh
```

```bash
#!/bin/bash

echo "Script name: $0"
echo "Total arguments: $#"
echo "All arguments: $@"
```

Run:

```bash
./args_demo.sh one two three
```

📌 **DevOps gold:** scripts configurable without editing code.

---

## ✅ Task 4: Install Packages via Script

### 🔹 install_packages.sh

```bash
vim install_packages.sh
```

```bash
#!/bin/bash

# Root check
if [ "$EUID" -ne 0 ]; then
  echo "Please run this script as root"
  exit 1
fi

PACKAGES=("nginx" "curl" "wget")

for pkg in "${PACKAGES[@]}"; do
  if dpkg -s "$pkg" &> /dev/null; then
    echo "$pkg is already installed"
  else
    echo "Installing $pkg..."
    apt-get update -y
    apt-get install -y "$pkg"
  fi
done
```

Run:

```bash
sudo ./install_packages.sh
```

📌 **This is real DevOps automation** (idempotent + safe).

---

## ✅ Task 5: Error Handling

### 🔹 safe_script.sh

```bash
vim safe_script.sh
```

```bash
#!/bin/bash
set -e

mkdir /tmp/devops-test || echo "Directory already exists"
cd /tmp/devops-test || echo "Failed to enter directory"
touch test.txt

echo "All steps completed successfully"
```

📌 `set -e` = **fail fast**
📌 `||` = graceful fallback message

---

## 📄 What to write in `day-17-scripting.md`

```md
# Day 17 – Shell Scripting: Loops, Arguments & Error Handling

## Scripts Created
- for_loop.sh
- count.sh
- countdown.sh
- greet.sh
- args_demo.sh
- install_packages.sh
- safe_script.sh

## Concepts Learned
- for and while loops for iteration
- Passing arguments using $1, $@, $#
- Writing idempotent install scripts
- Checking root access using EUID
- Error handling with set -e and ||

## Commands Used
chmod +x
for / while
dpkg -s
apt-get
set -e

## Key Takeaway
Shell scripts become powerful when they are reusable, safe, and fail predictably.
```

