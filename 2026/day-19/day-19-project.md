# 📄 `day-19-project.md`

---

# Day 19 – Log Rotation, Backup & Crontab Automation

## 🔹 Task 1 – Log Rotation Script

### `log_rotate.sh`

```bash
#!/bin/bash
set -euo pipefail

LOG_DIR="${1:-}"

if [ -z "$LOG_DIR" ]; then
  echo "Usage: $0 <log_directory>"
  exit 1
fi

if [ ! -d "$LOG_DIR" ]; then
  echo "Error: Directory does not exist."
  exit 1
fi

# Count files before
COMPRESS_COUNT=$(find "$LOG_DIR" -type f -name "*.log" -mtime +7 | wc -l)
DELETE_COUNT=$(find "$LOG_DIR" -type f -name "*.gz" -mtime +30 | wc -l)

# Compress .log files older than 7 days
find "$LOG_DIR" -type f -name "*.log" -mtime +7 -exec gzip {} \;

# Delete .gz files older than 30 days
find "$LOG_DIR" -type f -name "*.gz" -mtime +30 -delete

echo "Compressed files: $COMPRESS_COUNT"
echo "Deleted files: $DELETE_COUNT"
```
<img width="573" height="73" alt="image" src="https://github.com/user-attachments/assets/be5e7332-9a22-4238-911d-c47c60dcfa55" />

---

## 🔹 Task 2 – Backup Script

### `backup.sh`

```bash
#!/bin/bash
set -euo pipefail

SOURCE="${1:-}"
DEST="${2:-}"

if [ -z "$SOURCE" ] || [ -z "$DEST" ]; then
  echo "Usage: $0 <source_directory> <backup_destination>"
  exit 1
fi

if [ ! -d "$SOURCE" ]; then
  echo "Error: Source directory does not exist."
  exit 1
fi

mkdir -p "$DEST"

TIMESTAMP=$(date +%Y-%m-%d)
ARCHIVE="$DEST/backup-$TIMESTAMP.tar.gz"

tar -czf "$ARCHIVE" "$SOURCE"

if [ -f "$ARCHIVE" ]; then
  SIZE=$(du -h "$ARCHIVE" | cut -f1)
  echo "Backup created: $ARCHIVE"
  echo "Size: $SIZE"
else
  echo "Backup failed!"
  exit 1
fi

# Delete backups older than 14 days
find "$DEST" -type f -name "backup-*.tar.gz" -mtime +14 -delete
```

---

## 🔹 Task 3 – Crontab Understanding

Check existing cron:

```bash
crontab -l
```

### Cron Syntax

```
* * * * * command
│ │ │ │ │
│ │ │ │ └── Day of week (0-7)
│ │ │ └──── Month
│ │ └────── Day of month
│ └──────── Hour
└────────── Minute
```

### Required Cron Entries (Documented Only)

Run log rotation daily at 2 AM:

```
0 2 * * * /path/to/log_rotate.sh /var/log/myapp
```

Run backup every Sunday at 3 AM:

```
0 3 * * 0 /path/to/backup.sh /source /backup/location
```

Run health check every 5 minutes:

```
*/5 * * * * /path/to/health_check.sh
```

---

## 🔹 Task 4 – Combined Maintenance Script

### `maintenance.sh`

```bash
#!/bin/bash
set -euo pipefail

LOG_FILE="/var/log/maintenance.log"

log() {
  echo "$(date '+%Y-%m-%d %H:%M:%S') - $1" >> "$LOG_FILE"
}

log "Maintenance started"

# Call log rotation
if ./log_rotate.sh /var/log/myapp >> "$LOG_FILE" 2>&1; then
  log "Log rotation completed"
else
  log "Log rotation failed"
fi

# Call backup
if ./backup.sh /etc /backup >> "$LOG_FILE" 2>&1; then
  log "Backup completed"
else
  log "Backup failed"
fi

log "Maintenance finished"
```

### Cron entry (Daily 1 AM)

```
0 1 * * * /path/to/maintenance.sh
```

---

## 🔹 What I Learned

1. Real-world scripts must validate input and fail safely.
2. Automation should be idempotent and scheduled.
3. Logging is critical for troubleshooting scheduled jobs.
4. Cron is simple but powerful for server maintenance.
5. Backup + rotation = operational hygiene.

---
