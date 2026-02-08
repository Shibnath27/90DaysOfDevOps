## 📄 `day-14-networking.md`

````md
# Day 14 – Networking Fundamentals

## OSI vs TCP/IP (Quick Notes)

- **OSI Model (7 layers):**
  1. Physical – cables, NICs
  2. Data Link – MAC, ARP
  3. Network – IP routing
  4. Transport – TCP/UDP
  5. Session – session handling
  6. Presentation – encryption, formatting
  7. Application – HTTP, DNS, SSH

- **TCP/IP Model (4 layers):**
  - Link → Physical + Data Link
  - Internet → IP
  - Transport → TCP/UDP
  - Application → HTTP, HTTPS, DNS, SSH

- **Protocol mapping:**
  - IP → Network / Internet layer
  - TCP/UDP → Transport layer
  - HTTP/HTTPS, DNS → Application layer

- **Real example:**
  - `curl https://example.com`
  - Application (HTTP) → Transport (TCP) → Network (IP)

---
````
## Hands-on Networking Checks

### Identity
```bash
hostname -I
````
<img width="419" height="71" alt="image" src="https://github.com/user-attachments/assets/cffdab83-d32c-43e5-9b25-60f92db8b699" />


**Observation:** Shows the IP address assigned to the server.

---

### Reachability

```bash
ping google.com -c 4
```
<img width="734" height="173" alt="image" src="https://github.com/user-attachments/assets/9c49c5af-82a9-44ce-9aee-bd16791397c6" />


**Observation:** Packets received with low latency, no packet loss.

---

### Network Path

```bash
traceroute google.com
```
<img width="1136" height="170" alt="image" src="https://github.com/user-attachments/assets/9e480df7-0f67-4e37-a32b-5946a02a9e26" />


**Observation:** Shows intermediate hops; some hops may timeout due to firewall rules.

---

### Listening Ports

```bash
ss -tulpn
```
<img width="1117" height="259" alt="image" src="https://github.com/user-attachments/assets/8bcf8ce7-62ac-417a-aa71-39ac2e5e5882" />


**Observation:** SSH service is listening on port 22.

---

### DNS Resolution

```bash
dig google.com
```
<img width="574" height="343" alt="image" src="https://github.com/user-attachments/assets/c2d31bc3-bad7-4862-acd1-4eae46ee2a48" />


**Observation:** Domain resolves to multiple IP addresses (load balancing).

---

### HTTP Check

```bash
curl -I https://google.com
```
<img width="1145" height="272" alt="image" src="https://github.com/user-attachments/assets/b1633652-356a-4929-9c22-0739cfc9f17a" />

**Observation:** HTTP status `301/200` confirms web service is reachable.

---

### Connections Snapshot

```bash
netstat -an | head
```
<img width="679" height="187" alt="image" src="https://github.com/user-attachments/assets/fe1dcf36-1124-4058-aba5-57ffa445c4c0" />

**Observation:** Shows LISTEN and ESTABLISHED connections.

---

## Mini Task – Port Probe

* **Identified service:** SSH on port 22

```bash
nc -zv localhost 22
```

<img width="617" height="54" alt="image" src="https://github.com/user-attachments/assets/aca5090c-2640-495f-a35a-9ee68f1ce3c6" />

**Result:** Connection successful → service reachable.

**If failed, next checks:**

* `systemctl status ssh`
* Firewall rules (`ufw status` / `iptables -L`)

---

## Reflection

* **Fastest signal when something breaks:** `ping` or `curl`
* **If DNS fails:** Inspect Application layer (DNS)
* **If HTTP 500 occurs:** Application layer, then service logs

### Follow-up checks in real incidents:

1. Check service status (`systemctl status`)
2. Check logs (`journalctl -u <service>`)

```

