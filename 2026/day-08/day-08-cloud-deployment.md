## 🔹 Day 08 – Cloud Server Setup: Docker, Nginx & Web Deployment

---

## Part 1: Launch Cloud Instance & SSH Access

### Step 1: Create Cloud Instance

* Provider: **AWS EC2 (Ubuntu 22.04)** or **Utho**
* Instance type: `t3.micro` / equivalent
* Open inbound ports:

  * **22 (SSH)**
  * **80 (HTTP)**

### Step 2: Connect via SSH

```bash
# AWS
ssh -i your-key.pem ubuntu@<public-ip>

# Utho
ssh root@<public-ip>
```

<img width="1152" height="461" alt="image" src="https://github.com/user-attachments/assets/3d083c1c-1f78-4d58-b881-3676f0256f67" />

📸 **Screenshot:** Successful SSH login

---

## Part 2: Install Docker & Nginx

### Step 1: Update System

```bash
sudo apt update && sudo apt upgrade -y
```

### Step 2: Install Docker

```bash
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
```

Verify:

```bash
docker --version
```

### Step 3: Install Nginx

```bash
sudo apt install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
```

Verify:

```bash
systemctl status nginx
```

---

## Part 3: Security Group Configuration & Web Access

* Ensure **port 80** is allowed in Security Group / Firewall
* Open browser:

```
http://<your-public-ip>
```

✅ You should see **Nginx Welcome Page**

📸 **Screenshot:** Browser showing Nginx page

---

## Part 4: Extract Nginx Logs

### Step 1: View Logs

```bash
sudo tail -n 50 /var/log/nginx/access.log
```

### Step 2: Save Logs to File

```bash
sudo cp /var/log/nginx/access.log ~/nginx-logs.txt
```

Verify:

```bash
cat ~/nginx-logs.txt
```

📸 **Screenshot:** Log file content

---

### Step 3: Download Log File to Local Machine

```bash
# AWS
scp -i your-key.pem ubuntu@<public-ip>:~/nginx-logs.txt .

# Utho
scp root@<public-ip>:~/nginx-logs.txt .
```

---

## 📄 `day-08-cloud-deployment.md` (FINAL CONTENT)

```markdown
# Day 08 – Cloud Server Setup: Docker, Nginx & Web Deployment

## Commands Used
- ssh
- apt update && apt upgrade
- docker installation and verification
- nginx installation and service management
- tail, cp
- scp

## Challenges Faced
- Initially Nginx page was not accessible due to missing port 80 rule.
- Resolved by updating security group to allow HTTP traffic.

## What I Learned
- How to launch and access a cloud server via SSH
- Installing and managing services like Docker and Nginx
- Importance of security group/firewall configuration
- How to collect and export service logs
- Real-world cloud server deployment workflow
```

---

## ✅ Why this is strong

* Real cloud deployment
* Networking + Linux + logs
* Recruiter/interview friendly
* GitHub-proof practical work

---

### 🔜 Next

After Day 08, you are officially doing **real Cloud Engineer work**.

If you want next:

* ✍️ **Day 08 LinkedIn post**
* 📂 **GitHub commit message**
* 🚀 **Day 09 roadmap**

Say **next, RDX** 💪
