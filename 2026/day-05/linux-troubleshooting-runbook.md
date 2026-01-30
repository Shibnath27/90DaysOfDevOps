# Day 05 – Linux Troubleshooting Runbook

*(CPU, Memory, Disk, Network, Logs)*

## 🎯 Target Service

**Service:** `sshd` (SSH)
**Reason:** Critical access service, commonly troubleshot in production.

---

## 🔹 Environment Basics

```bash
uname -a
```
**Output**
<img width="988" height="51" alt="image" src="https://github.com/user-attachments/assets/ebbdc4d7-dd15-4e88-9885-e4210a8f7c00" />

* Confirms kernel version and architecture.

```bash
cat /etc/os-release
```
**Output**

<img width="387" height="321" alt="image" src="https://github.com/user-attachments/assets/c55f5aa1-18e9-44e8-8ece-c9bf65baaf42" />

* Confirms OS and version for compatibility checks.

---

## 🔹 Filesystem Sanity Check

```bash
mkdir /tmp/runbook-demo
cp /etc/hosts /tmp/runbook-demo/hosts-copy
ls -l /tmp/runbook-demo
```
**Output**

<img width="580" height="78" alt="image" src="https://github.com/user-attachments/assets/1f357d46-4e54-4593-b7b3-c0ed0367659d" />

* Confirms disk is writable and filesystem is healthy.

---

## 🔹 Snapshot: CPU & Memory

```bash
top
```
**Output**

<img width="734" height="555" alt="image" src="https://github.com/user-attachments/assets/44e01d8a-b15f-4845-aa47-021afd40af6b" />


* Observed normal CPU usage; no abnormal spikes from `sshd`.

```bash
ps -o pid,pcpu,pmem,comm -C sshd
```
**Output**

<img width="482" height="185" alt="image" src="https://github.com/user-attachments/assets/8e3af3a5-878b-4cde-8529-580e4959ea1c" />


* SSH process consuming minimal CPU and memory.

```bash
free -h
```
**Output**

<img width="668" height="77" alt="image" src="https://github.com/user-attachments/assets/f30c8057-77d3-4a2e-ad7a-bba6d3b3718f" />


* Sufficient free memory; no swap pressure.

---

## 🔹 Snapshot: Disk & IO

```bash
df -h
```
**Output**

<img width="545" height="145" alt="image" src="https://github.com/user-attachments/assets/6f5ed9c5-a7e1-4cdf-90d6-6b2df94fe43a" />


* Disk usage below critical threshold.

```bash
du -sh /var/log
```
**Output**

<img width="360" height="52" alt="image" src="https://github.com/user-attachments/assets/25e59a58-00ca-48f9-aaea-5a67fc92e89c" />


* Log directory size normal; no excessive growth.

---

## 🔹 `iostat` — Disk & CPU I/O Monitoring

**Used to check disk performance bottlenecks**

### Command

```bash
iostat -xz 1 5
```
**Output**

<img width="1149" height="537" alt="image" src="https://github.com/user-attachments/assets/2f35e8a8-53f9-4ca4-b2ad-a38d1bea8a81" />


### What it shows

* `%util` → Disk busy percentage
* `await` → Disk response time (latency)
* `r/s`, `w/s` → Read/write operations per second

### Red flags

* `%util` consistently **>80%**
* High `await` values → disk is slow

📌 **Use when:** app is slow, high I/O wait, database issues

---

## 🔹 `vmstat` — Memory, CPU & Context Switching

**High-level system health overview**

### Command

```bash
vmstat 1 5
```

**Output**

<img width="657" height="148" alt="image" src="https://github.com/user-attachments/assets/163bd7dc-7baa-4f0f-affa-dd5e75b92eb9" />


### Key fields

* `r` → runnable processes (CPU pressure)
* `si/so` → swap in/out (memory pressure)
* `us`, `sy`, `id`, `wa` → CPU usage breakdown

### Red flags

* High `wa` → waiting on disk
* Non-zero `si/so` → memory shortage

📌 **Use when:** system feels slow, memory leak suspicion

---

## 🔹 `dstat` — Combined Real-Time View

**All-in-one CPU, disk, network, memory**

### Command

```bash
dstat -cdngy
```
**Output**

<img width="539" height="372" alt="image" src="https://github.com/user-attachments/assets/558869ad-017e-4950-ba15-38c43362308c" />


### Shows

* CPU usage
* Disk read/write
* Network in/out
* Memory and paging

📌 **Use when:** live troubleshooting during incidents

---

## 🔹 Snapshot: Network

```bash
ss -tulpn | grep ssh
```

**Output**

<img width="766" height="74" alt="image" src="https://github.com/user-attachments/assets/14ab327b-3e61-4bd8-a3c9-d427693cd77b" />


* SSH listening on port 22 as expected.

```bash
curl -I localhost
```
**Output**

<img width="498" height="145" alt="image" src="https://github.com/user-attachments/assets/d67eb4c1-8324-488c-851d-4a53026c5ae2" />


* Network stack responsive (basic connectivity check).

---

## 🔹 Logs Reviewed

```bash
journalctl -u sshd -n 50 --no-pager
```
**Output**

<img width="979" height="524" alt="image" src="https://github.com/user-attachments/assets/9c0597fa-130c-44cb-8401-ef051ccc2119" />


* No authentication failures or service crashes in recent logs.

```bash
tail -n 50 /var/log/secure
```
**Output**

<img width="981" height="385" alt="image" src="https://github.com/user-attachments/assets/6b90b2ca-c111-49c2-8556-b323fae5c23d" />


* Normal login activity; no brute-force attempts observed.

---

## 🔹 Quick Findings

* SSH service is **running and stable**
* No CPU, memory, or disk pressure
* Network port open and responsive
* Logs show **no recent errors**

---

## 🔹 If This Worsens (Next Steps)

1. Restart service and monitor:

   ```bash
   systemctl restart sshd
   ```
2. Increase log verbosity for SSH debugging
3. Capture system metrics over time (`vmstat`, `iostat`)
4. Check firewall rules and fail2ban status



