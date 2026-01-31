# Day 07 – Linux File System Hierarchy & Practice

## Part 1: Linux File System Hierarchy

### / (root)
- Top-level directory; everything starts here.
```bash
ls -l /
````

<img width="539" height="361" alt="image" src="https://github.com/user-attachments/assets/70cd2d95-fe44-43c6-8eab-0cf94c1c3e0b" />


* Observed: bin, etc, home, var
* I would use this when navigating the system from scratch.

---

### /home

* Home directories for normal users.

```bash
ls -l /home
```

<img width="402" height="47" alt="image" src="https://github.com/user-attachments/assets/144bd6e0-ff01-4119-8cb1-cb7551523d30" />


* Observed: user directories
* I would use this when managing user files and permissions.

---

### /root

* Home directory of the root user.

```bash
ls -l /root
```

<img width="727" height="63" alt="image" src="https://github.com/user-attachments/assets/7ffc34bf-a721-4f47-bee4-f1a10be9894e" />


* Observed: root-owned files
* I would use this when working as administrator.

---

### /etc

* System-wide configuration files.

```bash
ls -l /etc | head
cat /etc/hostname
```

<img width="533" height="197" alt="image" src="https://github.com/user-attachments/assets/6a80bf77-0acb-43f8-a331-072fe87fa1ea" />



* Observed: hostname, ssh, systemd configs
* I would use this when troubleshooting configuration issues.

---

### /var/log

* Log files for system and services.

```bash
ls -l /var/log
du -sh /var/log/* 2>/dev/null | sort -h | tail -5
```

<img width="655" height="93" alt="image" src="https://github.com/user-attachments/assets/f71cad53-e7e9-4f01-aeef-49fc57a4c932" />


* Observed: journal, messages, secure logs
* I would use this during incident and error investigation.

---

### /tmp

* Temporary files; often cleared on reboot.

```bash
ls -l /tmp
```

<img width="971" height="113" alt="image" src="https://github.com/user-attachments/assets/ee498dec-d2d6-4fce-94eb-574a369dcd32" />


* Observed: temp directories/files
* I would use this for testing or short-lived files.

---

### /bin

* Essential system command binaries.

```bash
ls -l /bin | head
```

<img width="454" height="34" alt="image" src="https://github.com/user-attachments/assets/d6235dfc-2d44-4808-9e0d-f84ca805db45" />


* Observed: ls, cp, mv
* I would use this for basic system operations.

---

### /usr/bin

* User-level command binaries.

```bash
ls -l /usr/bin | head
```

<img width="466" height="166" alt="image" src="https://github.com/user-attachments/assets/36ce1e83-d8b1-45bd-8435-260b75e9ea0b" />


* Observed: curl, git, vim
* I would use this for application and user commands.

---

### /opt

* Optional or third-party software.

```bash
ls -l /opt
```

<img width="390" height="53" alt="image" src="https://github.com/user-attachments/assets/591ff5f9-ae71-417b-a8fe-a6cd070ef961" />


* Observed: vendor/application directories
* I would use this for managing custom installations.

---

## Hands-on Checks

### Check hostname config

```bash
cat /etc/hostname
```
<img width="394" height="49" alt="image" src="https://github.com/user-attachments/assets/7ca406b7-7cfc-48b1-980c-20f313b1b3bd" />

### Check home directory

```bash
ls -la ~
```
<img width="734" height="228" alt="image" src="https://github.com/user-attachments/assets/0bd6bfc7-2ec7-45e9-9412-cdf437b4203b" />

---


## Part 2: Scenario-Based Practice

---

### Scenario 1: Service Not Starting

**Problem:**
A web application service called `myapp` failed to start after a server reboot.

**Step 1**

```bash
systemctl status myapp
```

**Why:** Check whether the service is active, inactive, or failed.

**Step 2**

```bash
journalctl -u myapp -n 50
```

**Why:** View recent logs to understand why the service failed to start.

**Step 3**

```bash
systemctl is-enabled myapp
```

**Why:** Verify whether the service is configured to start automatically on boot.

**Step 4**

```bash
systemctl list-units --type=service | grep myapp
```

**Why:** Confirm the service exists and check its current state.

**What I learned:**
Service troubleshooting should always follow the flow: status → logs → boot configuration.

---

### Scenario 2: High CPU Usage

**Problem:**
The application server is slow after logging in via SSH.

**Step 1**

```bash
top
```

**Why:** Displays live CPU usage and helps identify high CPU–consuming processes.

**Step 2**

```bash
ps aux --sort=-%cpu | head -10
```

**Why:** Lists the top processes sorted by CPU usage.

**Step 3**

* Note the **PID** of the process using the highest CPU.
* Use the PID for further investigation if needed.

**What I learned:**
Always identify the problematic process before attempting any fix.

---

### Scenario 3: Finding Service Logs (docker)

**Problem:**
A developer asks where the logs for the `docker` service are located.

**Step 1**

```bash
systemctl status docker
```

**Why:** Confirms Docker is running and managed by systemd.

**Step 2**

```bash
journalctl -u docker -n 50
```

**Why:** Shows the most recent Docker service logs.

**Step 3**

```bash
journalctl -u docker -f
```

**Why:** Follows Docker logs in real time during debugging.

**What I learned:**
Systemd-managed services store logs in `journald`.

---

### Scenario 4: File Permissions Issue

**Problem:**
Script `/home/user/backup.sh` fails with “Permission denied”.

**Step 1**

```bash
ls -l /home/user/backup.sh
```

**Why:** Check current file permissions.

**Step 2**

```bash
chmod +x /home/user/backup.sh
```

**Why:** Add execute permission to the script.

**Step 3**

```bash
ls -l /home/user/backup.sh
```

**Why:** Verify that execute permission is applied.

**Step 4**

```bash
./backup.sh
```

**Why:** Confirm the script executes successfully.

**What I learned:**
A script must have execute (`x`) permission to run.

---



