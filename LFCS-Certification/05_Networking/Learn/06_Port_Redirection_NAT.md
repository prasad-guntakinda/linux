

# 🌐 **Network Address Translation (NAT) — Deep Explanation**

---

## 1️⃣ **Why NAT Exists (The Real Problem It Solves)**

### ❌ Problem without NAT

* IPv4 addresses are limited
* Private IPs (`10.x.x.x`, `192.168.x.x`) are **not routable on the Internet**
* ISPs give **1 public IP**, but you have **many internal machines**

Without NAT:

```
PC (192.168.1.10) → Internet ❌ (private IP dropped)
```

---

### ✅ Solution: NAT

NAT allows:

* Many private hosts → **1 public IP**
* Internal network hidden from Internet
* Port redirection to internal services

```
PC (192.168.1.10) → NAT Router → Internet
                     (changes source IP)
```

---

## 2️⃣ **Where NAT Happens in Linux (Very Important)**

Linux performs NAT in the **netfilter NAT table**.

### Netfilter hooks:

```
Incoming Packet
   |
   v
PREROUTING   → DNAT / Port Forwarding
   |
Routing Decision
   |
   v
POSTROUTING  → SNAT / MASQUERADE
   |
Outgoing Packet
```

> 🔑 **Memorize this for LFCS**

---

## 3️⃣ **Types of NAT — When & Why to Use Each**

---

## 🔵 3.1 SNAT (Source NAT)

### ✔ What it does

Changes the **source IP address** of outgoing packets.

```
Before:
192.168.1.10 → Google

After SNAT:
203.0.113.10 → Google
```

---

### ✔ When SNAT is needed

* Private network accessing Internet
* Multiple hosts behind one public IP
* Public IP is **static**

---

### ✔ Syntax (iptables)

```bash
iptables -t nat -A POSTROUTING \
  -s 192.168.1.0/24 \
  -o ens33 \
  -j SNAT --to-source 203.0.113.10
```

### 🔍 Syntax explained

| Option              | Meaning                      |
| ------------------- | ---------------------------- |
| `-t nat`            | Use NAT table                |
| `-A POSTROUTING`    | Apply after routing decision |
| `-s 192.168.1.0/24` | Match source network         |
| `-o ens33`          | Outgoing interface           |
| `-j SNAT`           | Target = Source NAT          |
| `--to-source`       | New source IP                |

---

## 🔵 3.2 MASQUERADE (Dynamic SNAT)

### ✔ What it does

Same as SNAT, but **auto-detects public IP**.

---

### ✔ When MASQUERADE is needed

* DHCP-based public IP
* Laptop, home broadband, cloud VMs
* You don’t know the public IP in advance

---

### ✔ Syntax

```bash
iptables -t nat -A POSTROUTING -o ens33 -j MASQUERADE
```

### 🔍 Syntax explained

| Part          | Meaning                        |
| ------------- | ------------------------------ |
| `POSTROUTING` | Change packet before it leaves |
| `-o ens33`    | Outgoing interface             |
| `MASQUERADE`  | Auto SNAT                      |

> 🔑 **Exam Tip:**
> MASQUERADE = SNAT with dynamic IP

---

## 🔴 3.3 DNAT (Destination NAT)

### ✔ What it does

Changes the **destination IP address** of incoming packets.

```
Internet → 203.0.113.10 → DNAT → 192.168.1.100
```

---

### ✔ When DNAT is needed

* Expose internal service to Internet
* Port forwarding
* Load balancers, reverse proxies

---

### ✔ Syntax (iptables)

```bash
iptables -t nat -A PREROUTING \
  -d 203.0.113.10 \
  -p tcp --dport 80 \
  -j DNAT --to-destination 192.168.1.100:80
```

### 🔍 Syntax explained

| Option             | Meaning             |
| ------------------ | ------------------- |
| `PREROUTING`       | Packet just arrived |
| `-d`               | Destination IP      |
| `--dport`          | Destination port    |
| `DNAT`             | Destination NAT     |
| `--to-destination` | New IP:Port         |

---

### ⚠ IMPORTANT

DNAT alone is not enough.

You must allow forwarding:

```bash
iptables -A FORWARD -p tcp -d 192.168.1.100 --dport 80 -j ACCEPT
```

---

## 🟣 3.4 Port Redirection (REDIRECT)

### ✔ What it does

Redirects traffic to **another port on the same machine**.

---

### ✔ When REDIRECT is needed

* Redirect 8080 → 80
* Transparent proxy
* Local testing

---

### ✔ Syntax

```bash
iptables -t nat -A PREROUTING \
  -p tcp --dport 8080 \
  -j REDIRECT --to-port 80
```

---

## 4️⃣ **firewalld NAT Concepts (High-Level)**

`firewalld` abstracts NAT using zones.

---

### Enable NAT (MASQUERADE)

```bash
firewall-cmd --add-masquerade --permanent
firewall-cmd --reload
```

Equivalent to:

```
POSTROUTING MASQUERADE
```

---

### Port Forwarding

```bash
firewall-cmd --add-forward-port=port=8080:proto=tcp:toport=80 --permanent
```

Redirect to another host:

```bash
firewall-cmd --add-forward-port=port=2222:proto=tcp:toaddr=192.168.1.50:toport=22 --permanent
```

---

## 5️⃣ **nftables NAT (Modern LFCS Systems)**

### Table & chain structure

```bash
table ip nat {
  chain prerouting {
    type nat hook prerouting priority -100;
  }
  chain postrouting {
    type nat hook postrouting priority 100;
  }
}
```

---

### MASQUERADE

```bash
nft add rule ip nat postrouting oifname "ens33" masquerade
```

---

### DNAT

```bash
nft add rule ip nat prerouting tcp dport 8080 dnat to 192.168.1.100:80
```

---

## 6️⃣ **Why NAT Needs Connection Tracking**

Linux tracks connections:

* Source IP
* Destination IP
* Source port
* Destination port

This ensures **return packets** are translated back correctly.

Without connection tracking:

* Replies would go to wrong IP
* Sessions would break

---

## 7️⃣ **Common NAT Mistakes (LFCS traps)**

❌ Forgetting to enable IP forwarding

```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```

❌ DNAT without FORWARD rule
❌ Wrong chain (SNAT in PREROUTING ❌)
❌ Wrong interface direction
❌ Forgetting firewall reload

---

## 8️⃣ **Decision Table — Which NAT Should I Use?**

| Scenario                    | NAT Type     |
| --------------------------- | ------------ |
| Private LAN → Internet      | MASQUERADE   |
| Static public IP            | SNAT         |
| Expose internal web server  | DNAT         |
| Redirect local port         | REDIRECT     |
| Multiple services on one IP | DNAT + ports |

---

## 9️⃣ **Mental Model (Exam Gold)**

> **If packet is going OUT → SNAT/MASQUERADE**
> **If packet is coming IN → DNAT/REDIRECT**

---

## 🔟 **One-Minute NAT Revision**

* NAT modifies IP/ports
* Happens in **nat table**
* PREROUTING → DNAT
* POSTROUTING → SNAT
* MASQUERADE = dynamic SNAT
* Port forwarding = DNAT
* Always enable IP forwarding
* Allow FORWARD chain

---
---

# Visual packet-flow diagrams**

Great choice 👍
Below are **clear, exam-oriented VISUAL packet-flow diagrams** using **accurate ASCII diagrams**, exactly suited for **LFCS understanding**.
(ASCII is intentional — it’s precise and matches real packet flow better than generic images.)

---

# 📦 **Visual Packet-Flow Diagrams for NAT (Linux / LFCS)**

These diagrams show **WHERE packets go**, **WHAT gets changed**, and **WHY**.

---

## 🟦 1️⃣ Packet Flow WITHOUT NAT (Problem Case)

```
[ PC ]
IP: 192.168.1.10
     |
     v
[ Router / Linux Box ]
     |
     v
[ Internet ]
```

### ❌ Problem

* Source IP = `192.168.1.10` (private IP)
* Internet routers **drop** this packet

👉 **Private IPs are not routable**

---

## 🟩 2️⃣ Packet Flow WITH SNAT / MASQUERADE (Internet Sharing)

### Scenario

* Internal network: `192.168.1.0/24`
* Linux router public IP: `203.0.113.10`
* Interface to internet: `ens33`

---

### 🔁 Outgoing Packet Flow

```
[ PC ]
SRC=192.168.1.10
DST=8.8.8.8
     |
     v
+-----------------------+
| Linux NAT Router      |
|                       |
| POSTROUTING (SNAT)    |
| SRC changed to        |
| 203.0.113.10          |
+-----------------------+
     |
     v
[ Internet ]
SRC=203.0.113.10
DST=8.8.8.8
```

### 🔁 Incoming Reply Packet

```
[ Internet ]
SRC=8.8.8.8
DST=203.0.113.10
     |
     v
+-----------------------+
| Linux NAT Router      |
| Connection Tracking   |
| DST restored to       |
| 192.168.1.10          |
+-----------------------+
     |
     v
[ PC ]
SRC=8.8.8.8
DST=192.168.1.10
```

### 🔑 Key Takeaways

* **SNAT happens in POSTROUTING**
* Connection tracking rewrites return packets
* MASQUERADE is the same, but dynamic IP

---

## 🟥 3️⃣ Packet Flow WITH DNAT (Port Forwarding)

### Scenario

* Public IP: `203.0.113.10`
* Internal web server: `192.168.1.100`
* Port: `80`

---

### 🔁 Incoming Packet Flow (DNAT)

```
[ Client on Internet ]
SRC=1.2.3.4
DST=203.0.113.10:80
     |
     v
+-----------------------+
| Linux NAT Router      |
| PREROUTING (DNAT)     |
| DST changed to        |
| 192.168.1.100:80     |
+-----------------------+
     |
     v
[ Internal Web Server ]
SRC=1.2.3.4
DST=192.168.1.100:80
```

### 🔁 Reply Packet Flow

```
[ Internal Web Server ]
SRC=192.168.1.100
DST=1.2.3.4
     |
     v
+-----------------------+
| Linux NAT Router      |
| POSTROUTING (SNAT)    |
| SRC changed to        |
| 203.0.113.10          |
+-----------------------+
     |
     v
[ Client ]
SRC=203.0.113.10
DST=1.2.3.4
```

### 🔑 Key Takeaways

* **DNAT happens in PREROUTING**
* **SNAT happens on reply**
* FORWARD chain must allow traffic

---

## 🟨 4️⃣ Packet Flow for Port Redirection (Same Host)

### Scenario

* Redirect `8080 → 80` on same machine

---

### 🔁 Packet Flow

```
[ Client ]
DST=Server:8080
     |
     v
+----------------------------+
| Linux Host                 |
| PREROUTING (REDIRECT)      |
| Port changed:              |
| 8080 → 80                  |
+----------------------------+
     |
     v
[ Local Service ]
Listening on port 80
```

### 🔑 Key Takeaways

* REDIRECT is a **special DNAT**
* Used for local services
* No FORWARD chain involved

---

## 🟪 5️⃣ Full NAT Table Flow (EXAM GOLD)

```
Incoming Packet
      |
      v
+------------------+
| PREROUTING       |  ← DNAT / REDIRECT
+------------------+
      |
      v
+------------------+
| Routing Decision |
+------------------+
      |
      v
+------------------+
| FORWARD / INPUT  |
+------------------+
      |
      v
+------------------+
| POSTROUTING      |  ← SNAT / MASQUERADE
+------------------+
      |
      v
Outgoing Packet
```

### 🔑 Memorize This Mapping

| NAT Type   | Chain       |
| ---------- | ----------- |
| DNAT       | PREROUTING  |
| REDIRECT   | PREROUTING  |
| SNAT       | POSTROUTING |
| MASQUERADE | POSTROUTING |

---

## 🟫 6️⃣ Combined Real-World Example (Most Common)

```
Internal PC (192.168.1.10)
        |
        v
   Linux Router
   ├─ PREROUTING (DNAT)  ← inbound services
   ├─ FORWARD
   └─ POSTROUTING (MASQUERADE) ← outbound internet
        |
        v
      Internet
```

This single Linux system:

* Shares internet
* Hosts port forwarding
* Performs NAT for many clients

---

## 🎯 7️⃣ LFCS Exam Mental Model (Very Important)

> 🧠 **Ask ONE question:**
> Is the packet **coming IN** or **going OUT**?

| Direction | NAT Action        |
| --------- | ----------------- |
| Coming IN | DNAT / REDIRECT   |
| Going OUT | SNAT / MASQUERADE |

---

## ✅ Final Visual Summary

```
OUTBOUND  → POSTROUTING → SNAT / MASQUERADE
INBOUND   → PREROUTING  → DNAT / REDIRECT
```

---

If you want next, I strongly recommend:

📌 **Hands-on NAT lab using dummy NICs (step-by-step)**
📌 **One-page NAT + firewall combined cheat sheet**
📌 **Troubleshooting flowchart (why NAT is not working)**

Just tell me 👍

