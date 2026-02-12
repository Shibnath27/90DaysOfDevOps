# 📄 `day-18-scripting.md`

```markdown
# Day 18 – Shell Scripting: Functions & Advanced Concepts

## Task 1 – Basic Functions

### functions.sh
```

```bash
#!/bin/bash

greet() {
    local name="$1"
    echo "Hello, $name!"
}

add() {
    local num1="$1"
    local num2="$2"
    echo "Sum: $((num1 + num2))"
}

greet "Shibnath"
add 5 7
```

```markdown
---

## Task 2 – Functions with System Checks

### disk_check.sh
```

```bash
#!/bin/bash

check_disk() {
    echo "Disk Usage:"
    df -h /
}

check_memory() {
    echo "Memory Usage:"
    free -h
}

main() {
    check_disk
    echo
    check_memory
}

main
```

```markdown
---

## Task 3 – Strict Mode

### strict_demo.sh
```

```bash
#!/bin/bash
set -euo pipefail

echo "Demonstrating strict mode"

# Undefined variable example (set -u)
echo "$UNDEFINED_VAR"

# Failing command example (set -e)
false

# Pipe failure example (set -o pipefail)
grep "text" non_existent_file | wc -l
```

### What Each Flag Does

* set -e → Exit immediately if any command fails.
* set -u → Exit if an undefined variable is used.
* set -o pipefail → Fail a pipeline if any command inside it fails (not just the last one).

---

## Task 4 – Local Variables

### local_demo.sh

````

```bash
#!/bin/bash

my_function() {
    local var="I am local"
    echo "$var"
}

my_function
echo "${var:-Variable not accessible outside function}"
````

---

## Task 5 – System Info Reporter

### system_info.sh

````

```bash
#!/bin/bash
set -euo pipefail

print_header() {
    echo "======================================"
    echo "$1"
    echo "======================================"
}

system_info() {
    print_header "System Information"
    hostname
    uname -a
}

uptime_info() {
    print_header "Uptime"
    uptime
}

disk_info() {
    print_header "Top 5 Largest Directories in /"
    du -h / 2>/dev/null | sort -hr | head -5
}

memory_info() {
    print_header "Memory Usage"
    free -h
}

cpu_info() {
    print_header "Top 5 CPU Processes"
    ps -eo pid,comm,%cpu --sort=-%cpu | head -6
}

main() {
    system_info
    uptime_info
    disk_info
    memory_info
    cpu_info
}

main
````

---

## Commands Used

* Functions
* local keyword
* set -euo pipefail
* df, free, ps, du
* arithmetic expansion $(( ))

---

## What I Learned

1. Functions make scripts modular and reusable.
2. Strict mode prevents silent failures — critical for production scripts.
3. Using local variables avoids unexpected side effects.
4. Clean structure (main + functions) makes scripts maintainable.

```
