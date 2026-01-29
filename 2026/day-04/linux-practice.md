# Day 04 – Linux Practice: Processes and Services

## 🔹 Process Checks

### 1. Check running processes

```bash
ps aux | head
```

* Shows currently running processes with CPU and memory usage.

### 2. Real-time process monitoring

```bash
top
```

* Used to identify high CPU or memory-consuming processes.

### 3. Find process by name

```bash
pgrep sshd
```

* Returns PID of the SSH service if running.

---

## 🔹 Service Checks (systemd)

### 4. Check SSH service status

```bash
systemctl status sshd
```

* Confirms whether SSH service is active and running.

### 5. List running services

```bash
systemctl list-units --type=service --state=running
```

* Lists all active systemd services.

---

## 🔹 Log Checks

### 6. View logs for SSH service

```bash
journalctl -u sshd --no-pager | tail -n 20
```

* Shows recent SSH-related log entries.

### 7. Check system logs

```bash
journalctl -xe
```

* Displays recent system errors and warnings.

---

## 🔹 Mini Troubleshooting Steps (SSH Example)

**Scenario:** SSH service not accessible

1. Check service status

   ```bash
   systemctl status sshd
   ```

2. Restart the service if needed

   ```bash
   systemctl restart sshd
   ```

3. Verify SSH is listening on port 22

   ```bash
   ss -tuln | grep 22
   ```

4. Review logs for errors

   ```bash
   journalctl -u sshd
   ```

---

## 🔹 Key Learning

* `ps` and `top` help identify process-level issues
* `systemctl` is the primary tool for service management
* `journalctl` is essential for root cause analysis
* Simple checks save time during production incidents

---


