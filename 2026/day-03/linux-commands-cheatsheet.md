# Linux Commands Cheat Sheet

*(Day 03 – Practice Notes)*

## 🔹 Process Management

* `ps aux` – List all running processes with details
* `top` – Real-time CPU and memory usage
* `htop` – Enhanced interactive process viewer
* `pgrep <name>` – Find PID by process name
* `kill <PID>` – Gracefully stop a process
* `kill -9 <PID>` – Force kill a stuck process
* `uptime` – System running time and load average
* `systemctl status <service>` – Check service state
* `systemctl restart <service>` – Restart a service

---

## 🔹 File System & Disk

* `ls -lh` – List files with size details
* `pwd` – Show current directory
* `cd <dir>` – Change directory
* `df -h` – Disk space usage
* `du -sh <dir>` – Directory size
* `mount` – Show mounted filesystems
* `lsblk` – View disk and partition layout
* `chmod 755 <file>` – Change file permissions
* `chown user:group <file>` – Change ownership

---

## 🔹 Networking & Troubleshooting

* `ip addr` – Show IP addresses
* `ip route` – View routing table
* `ping <host>` – Test network connectivity
* `ss -tuln` – List listening ports
* `curl <url>` – Test HTTP/HTTPS response
* `dig <domain>` – DNS lookup

---

## 🔹 Logs & System Info

* `journalctl -xe` – View recent system logs
* `journalctl -u <service>` – Service-specific logs
* `free -h` – Memory usage
* `uname -a` – Kernel and system info

---

## 🔹 Practical Notes

* High CPU → `top` / `htop`
* Service down → `systemctl status` + `journalctl`
* Disk full → `df -h` + `du -sh`
* Network issue → `ip addr`, `ping`, `ss`, `curl`

---

