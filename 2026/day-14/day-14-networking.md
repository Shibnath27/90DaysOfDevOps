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

## Hands-on Networking Checks

### Identity
```bash
hostname -I
````

**Observation:** Shows the IP address assigned to the server.

---

### Reachability

```bash
ping google.com -c 4
```

**Observation:** Packets received with low latency, no packet loss.

---

### Network Path

```bash
traceroute google.com
```

**Observation:** Shows intermediate hops; some hops may timeout due to firewall rules.

---

### Listening Ports

```bash
ss -tulpn
```

**Observation:** SSH service is listening on port 22.

---

### DNS Resolution

```bash
dig google.com
```

**Observation:** Domain resolves to multiple IP addresses (load balancing).

---

### HTTP Check

```bash
curl -I https://google.com
```

**Observation:** HTTP status `301/200` confirms web service is reachable.

---

### Connections Snapshot

```bash
netstat -an | head
```

**Observation:** Shows LISTEN and ESTABLISHED connections.

---

## Mini Task – Port Probe

* **Identified service:** SSH on port 22

```bash
nc -zv localhost 22
```

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

