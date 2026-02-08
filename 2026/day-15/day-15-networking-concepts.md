# Day 15 – Networking Concepts: DNS, IP, Subnets & Ports

## Task
Build on Day 14 by understanding the building blocks of networking every DevOps engineer must know.

---

## Task 1: DNS – How Names Become IPs

### 1. What happens when you type `google.com` in a browser?
When I type `google.com`, my system first checks the local DNS cache.  
If not found, it queries a DNS resolver, which contacts root servers, then TLD servers, and finally the authoritative DNS server.  
The authoritative server returns the IP address, and the browser connects to that IP using TCP/IP.

---

### 2. DNS Record Types

- **A** – Maps a domain name to an IPv4 address  
- **AAAA** – Maps a domain name to an IPv6 address  
- **CNAME** – Alias that points one domain to another domain  
- **MX** – Specifies mail servers for a domain  
- **NS** – Defines authoritative name servers for a domain  

---

### 3. `dig google.com`
```bash
dig google.com
````

* **A Record:** 142.250.x.x (example)
* **TTL:** ~300 seconds (may vary)

TTL defines how long the DNS response is cached.

---

## Task 2: IP Addressing

### 1. What is an IPv4 address?

An IPv4 address is a 32-bit address written as four octets separated by dots.
Example: `192.168.1.10`
Each octet ranges from 0 to 255.

---

### 2. Public vs Private IP

* **Public IP:** Accessible over the internet
  Example: `8.8.8.8`
* **Private IP:** Used inside internal networks
  Example: `192.168.1.10`

---

### 3. Private IP Ranges

* `10.0.0.0 – 10.255.255.255`
* `172.16.0.0 – 172.31.255.255`
* `192.168.0.0 – 192.168.255.255`

---

### 4. Identify Private IP

```bash
ip addr show
```
<img width="875" height="281" alt="image" src="https://github.com/user-attachments/assets/dd6c48b6-cd8b-48d4-9151-cacbcad8cb4a" />


**Observation:** My system IP belongs to a private IP range.

---

## Task 3: CIDR & Subnetting

### 1. What does `/24` mean?

`/24` means the first 24 bits are used for the network and the remaining bits are for host addresses.

---

### 2. Usable Hosts

* **/24:** 254 usable hosts
* **/16:** 65,534 usable hosts
* **/28:** 14 usable hosts

(Usable hosts = Total IPs − Network − Broadcast)

---

### 3. Why do we subnet?

Subnetting helps reduce broadcast traffic, improves security, organizes networks logically, and optimizes IP address usage.

---

### 4. CIDR Table

| CIDR | Subnet Mask     | Total IPs | Usable Hosts |
| ---- | --------------- | --------- | ------------ |
| /24  | 255.255.255.0   | 256       | 254          |
| /16  | 255.255.0.0     | 65,536    | 65,534       |
| /28  | 255.255.255.240 | 16        | 14           |

---

## Task 4: Ports – The Doors to Services

### 1. What is a port?

A port is a logical communication endpoint that allows multiple services to run on the same IP address.

---

### 2. Common Ports

| Port  | Service |
| ----- | ------- |
| 22    | SSH     |
| 80    | HTTP    |
| 443   | HTTPS   |
| 53    | DNS     |
| 3306  | MySQL   |
| 6379  | Redis   |
| 27017 | MongoDB |

---

### 3. Listening Ports

```bash
ss -tulpn
```
<img width="1131" height="292" alt="image" src="https://github.com/user-attachments/assets/1118b64e-027d-4937-a333-25aa21b6ada6" />

**Observed examples:**

* Port 22 → SSH service
* Port 80/443 → Web service (if running)

---

## Task 5: Putting It Together

### 1. `curl http://myapp.com:8080`

This involves DNS resolution, IP routing, TCP connection, and HTTP communication over port 8080.

---

### 2. App can't reach DB at `10.0.1.50:3306`

I would first check network reachability, port accessibility, database service status, and firewall or security rules.

---

## What I Learned

1. DNS resolution is a hierarchical and cached process
2. CIDR and subnetting control IP allocation and network design
3. Ports are essential for service-level communication and troubleshooting
