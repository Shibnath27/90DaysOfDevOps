# 📄 `day-20-solution.md`

---

# Day 20 – Log Analyzer & Report Generator

## 🔹 Objective

Automate log analysis and generate structured daily reports.

# 📜 `log_analyzer.sh`

```bash
#!/bin/bash
set -euo pipefail

# ---------------------------
# Input Validation
# ---------------------------
LOG_FILE="${1:-}"

if [ -z "$LOG_FILE" ]; then
    echo "Usage: $0 <log_file_path>"
    exit 1
fi

if [ ! -f "$LOG_FILE" ]; then
    echo "Error: File does not exist."
    exit 1
fi

DATE=$(date +%Y-%m-%d)
REPORT="log_report_${DATE}.txt"

TOTAL_LINES=$(wc -l < "$LOG_FILE")

# ---------------------------
# Error Count
# ---------------------------
ERROR_COUNT=$(grep -Eci "ERROR|Failed" "$LOG_FILE" || true)

echo "Total Errors: $ERROR_COUNT"

# ---------------------------
# Critical Events
# ---------------------------
CRITICAL_EVENTS=$(grep -n "CRITICAL" "$LOG_FILE" || true)

echo
echo "--- Critical Events ---"
if [ -n "$CRITICAL_EVENTS" ]; then
    echo "$CRITICAL_EVENTS"
else
    echo "No critical events found."
fi

# ---------------------------
# Top 5 Error Messages
# ---------------------------
TOP_ERRORS=$(grep -Ei "ERROR" "$LOG_FILE" \
    | sed -E 's/^[0-9:-]+\s+[0-9:]+\s+//' \
    | sort \
    | uniq -c \
    | sort -rn \
    | head -5 || true)

echo
echo "--- Top 5 Error Messages ---"
if [ -n "$TOP_ERRORS" ]; then
    echo "$TOP_ERRORS"
else
    echo "No error messages found."
fi

# ---------------------------
# Generate Summary Report
# ---------------------------
{
echo "Log Analysis Report"
echo "===================="
echo "Date: $DATE"
echo "Log File: $LOG_FILE"
echo "Total Lines Processed: $TOTAL_LINES"
echo "Total Error Count: $ERROR_COUNT"
echo
echo "--- Top 5 Error Messages ---"
echo "${TOP_ERRORS:-No errors found}"
echo
echo "--- Critical Events ---"
echo "${CRITICAL_EVENTS:-No critical events found}"
} > "$REPORT"

echo
echo "Report generated: $REPORT"

# ---------------------------
# Optional Archive
# ---------------------------
ARCHIVE_DIR="archive"

mkdir -p "$ARCHIVE_DIR"
mv "$LOG_FILE" "$ARCHIVE_DIR/"

echo "Log file moved to $ARCHIVE_DIR/"
```

---



## 🔹 Approach

### 1️⃣ Input Validation

* Accepted log file path as argument
* Exited safely if missing or invalid
* Used `set -euo pipefail` for strict error handling

---

### 2️⃣ Error Counting

Used:

```bash
grep -Eci "ERROR|Failed"
```

This counts all error-related lines case-insensitively.

---

### 3️⃣ Critical Event Extraction

Used:

```bash
grep -n "CRITICAL"
```

This prints line numbers along with matching events.

---

### 4️⃣ Top 5 Error Messages

Pipeline used:

```bash
grep -Ei "ERROR" logfile \
| sort \
| uniq -c \
| sort -rn \
| head -5
```

This identifies most frequent recurring issues.

---

### 5️⃣ Summary Report Generation

Generated file:

```
log_report_<YYYY-MM-DD>.txt
```

Included:

* Date
* Log file name
* Total lines processed
* Error count
* Top 5 errors
* Critical events

---

### 6️⃣ Archiving

* Created `archive/` directory if missing
* Moved processed log file after analysis
* Confirmed movement

---

## 🔹 Key Learnings

* Production log analysis requires structure, not just grep.
* Sorting + counting reveals recurring failure patterns.
* Archiving prevents reprocessing.
* Reports improve visibility and accountability.

