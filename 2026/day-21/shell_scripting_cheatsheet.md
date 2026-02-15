# 📄 `shell_scripting_cheatsheet.md`

---

# 🐚 Shell Scripting Cheat Sheet – DevOps Quick Reference

---

## 🔥 Quick Reference Table

| Topic    | Key Syntax               | Example                            |
| -------- | ------------------------ | ---------------------------------- |
| Variable | `VAR="value"`            | `NAME="DevOps"`                    |
| Argument | `$1, $2`                 | `./script.sh arg1`                 |
| If       | `if [ condition ]; then` | `if [ -f file ]; then`             |
| For Loop | `for i in list; do`      | `for i in 1 2 3; do`               |
| Function | `name() { ... }`         | `greet() { echo "Hi"; }`           |
| Grep     | `grep pattern file`      | `grep -i "error" log.txt`          |
| Awk      | `awk '{print $1}' file`  | `awk -F: '{print $1}' /etc/passwd` |
| Sed      | `sed 's/old/new/g' file` | `sed -i 's/foo/bar/g' config.txt`  |

---

# 1️⃣ Basics

## Shebang

```bash
#!/bin/bash
```

Defines interpreter. Ensures script runs with Bash.

---

## Running a Script

```bash
chmod +x script.sh
./script.sh
bash script.sh
```

---

## Comments

```bash
# Single line comment
echo "Hello" # Inline comment
```

---

## Variables

```bash
VAR="value"
echo $VAR
echo "$VAR"
echo '$VAR'
```

---

## Read User Input

```bash
read -p "Enter name: " NAME
echo "Hello $NAME"
```

---

## Command-Line Arguments

```bash
echo $0   # script name
echo $1   # first argument
echo $#   # total arguments
echo $@   # all arguments
echo $?   # last command exit code
```

---

# 2️⃣ Operators & Conditionals

## String Comparisons

```bash
[ "$A" = "$B" ]
[ "$A" != "$B" ]
[ -z "$A" ]   # empty
[ -n "$A" ]   # not empty
```

---

## Integer Comparisons

```bash
[ $A -eq $B ]
[ $A -gt $B ]
[ $A -lt $B ]
[ $A -ne $B ]
```

---

## File Tests

```bash
[ -f file ]   # regular file
[ -d dir ]    # directory
[ -e file ]   # exists
[ -r file ]   # readable
[ -w file ]   # writable
[ -x file ]   # executable
[ -s file ]   # not empty
```

---

## If Statement

```bash
if [ condition ]; then
    echo "True"
elif [ other ]; then
    echo "Else If"
else
    echo "False"
fi
```

---

## Logical Operators

```bash
cmd1 && cmd2
cmd1 || echo "Failed"
! command
```

---

## Case Statement

```bash
case $VAR in
  start) echo "Starting" ;;
  stop)  echo "Stopping" ;;
  *)     echo "Invalid" ;;
esac
```

---

# 3️⃣ Loops

## For Loop (List)

```bash
for i in 1 2 3; do
  echo $i
done
```

## C-Style Loop

```bash
for ((i=1; i<=5; i++)); do
  echo $i
done
```

---

## While Loop

```bash
while [ $COUNT -lt 5 ]; do
  echo $COUNT
  ((COUNT++))
done
```

---

## Until Loop

```bash
until [ $COUNT -gt 5 ]; do
  echo $COUNT
  ((COUNT++))
done
```

---

## Loop Control

```bash
break
continue
```

---

## Loop Over Files

```bash
for file in *.log; do
  echo "$file"
done
```

---

## Loop Over Command Output

```bash
ls | while read line; do
  echo "$line"
done
```

---

# 4️⃣ Functions

## Define Function

```bash
greet() {
  echo "Hello $1"
}
```

## Call Function

```bash
greet "DevOps"
```

## Return vs Echo

```bash
return 1   # exit status
echo "value"  # output
```

## Local Variables

```bash
myfunc() {
  local VAR="inside"
}
```

---

# 5️⃣ Text Processing Commands

## grep

```bash
grep "error" file
grep -i "error" file
grep -r "error" /var/log
grep -c "error" file
grep -n "error" file
grep -v "info" file
grep -E "ERROR|FAIL" file
```

---

## awk

```bash
awk '{print $1}' file
awk -F: '{print $1}' /etc/passwd
awk '/ERROR/ {print $0}' file
awk 'BEGIN{print "Start"} END{print "End"}' file
```

---

## sed

```bash
sed 's/old/new/g' file
sed -i 's/foo/bar/g' file
sed '3d' file
```

---

## cut

```bash
cut -d: -f1 /etc/passwd
```

---

## sort

```bash
sort file
sort -n file
sort -r file
sort -u file
```

---

## uniq

```bash
uniq file
uniq -c file
```

---

## tr

```bash
tr 'a-z' 'A-Z'
tr -d '\n'
```

---

## wc

```bash
wc -l file
wc -w file
wc -c file
```

---

## head / tail

```bash
head -5 file
tail -5 file
tail -f logfile
```

---

# 6️⃣ Useful One-Liners

## Delete files older than 7 days

```bash
find /path -type f -mtime +7 -delete
```

## Count lines in all .log files

```bash
wc -l *.log
```

## Replace string across multiple files

```bash
sed -i 's/old/new/g' *.conf
```

## Check if service is running

```bash
systemctl is-active nginx
```

## Monitor disk usage alert

```bash
df -h | awk '$5+0 > 80 {print "High usage:", $0}'
```

## Tail log & filter errors

```bash
tail -f app.log | grep --line-buffered ERROR
```

---

# 7️⃣ Error Handling & Debugging

## Exit Codes

```bash
exit 0
exit 1
echo $?
```

---

## Strict Mode

```bash
set -e      # exit on error
set -u      # error on unset variable
set -o pipefail  # catch pipe failures
set -x      # debug mode
```

---

## Trap

```bash
trap 'echo "Cleaning up"; rm -f temp.txt' EXIT
```

---

# 🧠 DevOps Reminder

* Always quote variables: `"$VAR"`
* Use `set -euo pipefail` in production scripts
* Validate inputs
* Log output
* Prefer idempotent operations

---

This cheat sheet is structured for **daily operational use**, not theory.

