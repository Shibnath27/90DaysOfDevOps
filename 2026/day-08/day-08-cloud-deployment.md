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
<img width="1120" height="610" alt="image" src="https://github.com/user-attachments/assets/bfc98094-9ec6-4c7d-b761-91a159ea85d3" />

### Step 2: Install Docker

```bash
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
```
<img width="1144" height="417" alt="image" src="https://github.com/user-attachments/assets/3888cf68-6ba8-434a-9e92-bf506be2d165" />

Verify:

```bash
docker --version
```
<img width="512" height="58" alt="image" src="https://github.com/user-attachments/assets/b320351a-ccb5-4311-b99c-0eb964289128" />

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
<img width="1066" height="323" alt="image" src="https://github.com/user-attachments/assets/393c5b52-73a6-4538-a5e6-37b39b4750ee" />

---

## Part 3: Security Group Configuration & Web Access

* Ensure **port 80** is allowed in Security Group / Firewall
* Open browser:

```
http://<your-public-ip>
```

✅ You should see **Nginx Welcome Page**

<img width="1355" height="373" alt="image" src="https://github.com/user-attachments/assets/dfab4f6d-84a9-4b93-a81b-da83408e9ebd" />

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
<img width="1146" height="95" alt="image" src="https://github.com/user-attachments/assets/f848ac41-eb9b-4639-b7d4-e8e228325c69" />

Verify:

```bash
sudo cat ~/nginx-logs.txt
```
<img width="1142" height="99" alt="image" src="https://github.com/user-attachments/assets/f6047cc4-5bd3-408f-bcf6-90ba75383003" />

📸 **Screenshot:** Log file content

---

### Step 3: Download Log File to Local Machine

```bash
# AWS
scp -i your-key.pem ubuntu@<public-ip>:~/nginx-logs.txt .

# Utho
scp root@<public-ip>:~/nginx-logs.txt .
```
<img width="1151" height="172" alt="image" src="https://github.com/user-attachments/assets/450c68bd-318b-4732-ba1c-5937afcc11f3" />

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

