# Day 04 – Linux Practice: Processes and Services

## 🔹 Process Checks

### 1. Check running processes

```bash
ps aux | head
```
**Output:**
<img width="1150" height="201" alt="image" src="https://github.com/user-attachments/assets/5e5b9ea1-f37b-4d59-b6e9-3ea45e12caab" />

* Shows currently running processes with CPU and memory usage.

### 2. Real-time process monitoring

```bash
top
```
**Output:**
<img width="1130" height="552" alt="image" src="https://github.com/user-attachments/assets/0f8eabd8-cb48-4a59-b461-57cbc2b435bb" />

* Used to identify high CPU or memory-consuming processes.

### 3. Find process by name

```bash
pgrep sshd
```
**Output:**
<img width="398" height="90" alt="image" src="https://github.com/user-attachments/assets/e8a095f9-2b8d-46ac-a58b-694726a5f461" />

* Returns PID of the SSH service if running.

---

## 🔹 Service Checks (systemd)

### 4. Check SSH service status

```bash
systemctl status sshd
```
**Output:**
<img width="968" height="381" alt="image" src="https://github.com/user-attachments/assets/9c096d2b-4653-489e-b0e9-5b7bb2d75987" />

* Confirms whether SSH service is active and running.

### 5. List running services

```bash
systemctl list-units --type=service --state=running
```
**Output:**

<img width="725" height="361" alt="image" src="https://github.com/user-attachments/assets/3ef6a85f-8166-47d1-9cc2-c917ebe57f29" />

* Lists all active systemd services.

---

## 🔹 Log Checks

### 6. View logs for SSH service

```bash
journalctl -u sshd --no-pager | tail -n 20
```
**Output:**
<img width="986" height="214" alt="image" src="https://github.com/user-attachments/assets/b49e6565-d29b-4ff4-a188-e72030be81f1" />

* Shows recent SSH-related log entries.

### 7. Check system logs

```bash
journalctl -xe
```
**Output:**
<img width="1140" height="519" alt="image" src="https://github.com/user-attachments/assets/9a320bd2-e480-44c8-888e-b621daf445a8" />

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


