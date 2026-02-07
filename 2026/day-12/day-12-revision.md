
# Day 12 – Breather & Revision (Days 01–11)

## Goal
Take a pause to consolidate Linux fundamentals learned from Days 01–11 and strengthen retention before moving forward.

---

## Mindset & Plan Review
- My original goal from Day 01 (build strong Linux + Cloud fundamentals for DevOps/Cloud roles) is still valid.
- Hands-on practice helped more than just reading or watching videos.
- Going forward, I will focus more on practical labs and GitHub documentation.

---

## Processes & Services Review
Commands rerun today:
- ps aux | head -5
- systemctl status ssh
- journalctl -u ssh -n 10

Observations:
- SSH service is active and running.
- No critical errors found in recent logs.
- Process listing helps quickly identify system activity.

---

## File Skills Practice
Quick operations practiced:
- echo "revision test" >> revise.txt
- chmod 640 revise.txt
- ls -l revise.txt
- cp revise.txt revise-backup.txt
- mkdir revision-test

This helped reinforce file permissions and basic file handling.

---

## Cheat Sheet Refresh (Day 03)
Top 5 commands I would use first during an incident:
- top
- df -h
- free -h
- systemctl status <service>
- journalctl -u <service>

---

## User / Group Sanity Check
Scenario practiced:
- Verified user identity and ownership using:
  - id tokyo
  - ls -l revise.txt
- Changed group ownership and rechecked permissions successfully.

---

## Mini Self-Check

### 1. Which 3 commands save me the most time right now, and why?
- top – quickly identifies high CPU or memory usage.
- systemctl status – gives instant service health status.
- journalctl – helps trace errors and recent service activity.

---

### 2. How do I check if a service is healthy?
Commands I run first:
```bash
systemctl status <service>
journalctl -u <service> -n 20
ps aux | grep <service>
````

---

### 3. How do I safely change ownership and permissions without breaking access?

I always check current permissions first, then apply minimal changes.

Example:

```bash
ls -l file.txt
sudo chown user:group file.txt
chmod 640 file.txt
```

---

### 4. What will I focus on improving in the next 3 days?

* Docker fundamentals (images, containers, basic commands)
* Running containers on a cloud server
* Understanding how containers are used in real DevOps workflows

---

## Key Takeaway

Revision helped reinforce Linux fundamentals like permissions, ownership, and service troubleshooting, making me more confident before moving into Docker and containerization.



