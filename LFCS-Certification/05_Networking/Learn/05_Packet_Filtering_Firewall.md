
# 🔥 **Packet Filtering Concept — Firewall Explained**

**Packet filtering** means inspecting network packets (source, destination, ports, protocol) and deciding whether to:

* **Allow (ACCEPT)**
* **Block (DROP)**
* **Reject (REJECT)** (send an error response)

Linux firewalls apply rules to control incoming/outgoing packets based on:

* IP address
* Port
* Protocol (TCP/UDP/ICMP)
* Network Interface
* Connection state

Linux firewalls are implemented through:

* **firewalld** (modern RHEL/CentOS/Rocky Linux/Fedora)
* **nftables** (new backend)
* **iptables** (legacy backend)

---

# 🟦 **1. Packet Filtering Using firewalld**

`firewalld` uses **zones** + **services** + **ports**.

Check status:

```bash
sudo firewall-cmd --state
```

### ✔ List all open ports + services

```bash
sudo firewall-cmd --list-all
```

---

# 🟩 **Example 1: Allow a Service (HTTP on port 80)**

```bash
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --reload
```

OR open port manually:

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --reload
```

This creates a packet-filter rule:

> Allow TCP packets destined for port 80.

Test:

```bash
ss -tuln | grep 80
```

---

# 🟧 **Example 2: Block a Port (Disable SSH temporarily)**

```bash
sudo firewall-cmd --remove-service=ssh --permanent
sudo firewall-cmd --reload
```

This blocks:

* All incoming packets on port **22/tcp**

---

# 🟥 **Example 3: Allow Traffic from a Specific Source (IP-based Filtering)**

```bash
sudo firewall-cmd --add-rich-rule='rule family="ipv4" \
 source address="192.168.1.50" port port="22" protocol="tcp" accept' \
 --permanent
sudo firewall-cmd --reload
```

Meaning:

* Only **192.168.1.50** can SSH into this machine.
* All other IPs are blocked.

---

# 🟨 **Example 4: Block a Host Completely**

```bash
sudo firewall-cmd --add-rich-rule='rule family="ipv4" \
 source address="192.168.1.100" drop' --permanent
sudo firewall-cmd --reload
```

This **drops all packets** from the host silently.

---

# 🟪 **Example 5: Reject Packets with Error Response**

```bash
sudo firewall-cmd --add-rich-rule='rule family="ipv4" \
 source address="203.0.113.10" reject' --permanent
```

Difference:

| DROP            | REJECT                               |
| --------------- | ------------------------------------ |
| Silent discard  | Sends ICMP “destination unreachable” |
| Harder to debug | Easier for clients to detect deny    |

---

# 🟫 **Example 6: Limit Traffic (Rate Limiting)**

Prevent brute-force attacks:

```bash
sudo firewall-cmd --add-rich-rule='
rule service name="ssh" log prefix="ssh-limit" limit value="5/m" accept' \
--permanent
```

This allows only **5 SSH connection attempts per minute**.

---

# 🟦 **2. Packet Filtering Using iptables (Legacy)**

Still important for concepts & interviews.

### ✔ List rules:

```bash
sudo iptables -L -n -v
```

---

# 🟩 **Example 1: Allow port 80**

```bash
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```

---

# 🟧 **Example 2: Block SSH from specific IP**

```bash
sudo iptables -A INPUT -s 192.168.1.55 -p tcp --dport 22 -j DROP
```

---

# 🟥 **Example 3: Allow ICMP Ping**

```bash
sudo iptables -A INPUT -p icmp -j ACCEPT
```

Block ping:

```bash
sudo iptables -A INPUT -p icmp -j DROP
```

---

# 🟨 **Example 4: Default Deny Policy**

```bash
sudo iptables -P INPUT DROP
sudo iptables -P FORWARD DROP
```

Meaning:

* Only explicitly allowed packets get in.

---

# 🟪 **Example 5: NAT (SNAT/DNAT) – High-Level Concept**

### SNAT Example:

```bash
sudo iptables -t nat -A POSTROUTING -o ens33 -j MASQUERADE
```

Used for:

* Internet sharing
* Virtual machines needing NAT

### DNAT Example (Port Forwarding):

```bash
sudo iptables -t nat -A PREROUTING -p tcp \
 --dport 8080 -j DNAT --to-destination 192.168.1.100:80
```

---

# 🟫 **3. Packet Filtering Using nftables (Modern)**

List rules:

```bash
sudo nft list ruleset
```

Allow port 80:

```bash
sudo nft add rule inet filter input tcp dport 80 accept
```

Block IP:

```bash
sudo nft add rule inet filter input ip saddr 10.10.10.5 drop
```

---

# 🧠 **How Packet Filtering Works (Simplified)**

```
Incoming Packet
      |
      v
+---------------------+
| Firewall Rules      |
| firewalld / iptables|
+---------------------+
      |
      +--> ACCEPT → Forward to Application
      |
      +--> DROP   → Silently ignore
      |
      +--> REJECT → Send error back
```

Firewall checks packets using:

* Source IP
* Destination IP
* Port
* Protocol
* Connection state (NEW, ESTABLISHED)

---

# 🧪 **Real-World Scenarios**

### 🟩 Allow Web, Block SSH

```bash
firewall-cmd --add-service=http --permanent
firewall-cmd --remove-service=ssh --permanent
firewall-cmd --reload
```

### 🟥 Allow SSH only from internal network

```bash
firewall-cmd --add-rich-rule='rule family="ipv4" source address="192.168.1.0/24" service name="ssh" accept' --permanent
```

### 🟧 Block brute-force attacks on SSH

Rate limiting prevents flooding.

### 🟨 Block all traffic except a known IP

```bash
firewall-cmd --set-default-zone=drop
firewall-cmd --add-rich-rule='rule family="ipv4" source address="10.0.0.5" accept'
```

---

# 🎯 **Packet Filtering Summary**

| Concept    | Meaning                                        |
| ---------- | ---------------------------------------------- |
| ACCEPT     | Allow packet                                   |
| DROP       | Silently discard                               |
| REJECT     | Block + send error                             |
| Zone       | Group of firewall rules (firewalld)            |
| Service    | Predefined rules (http, ssh, dns)              |
| Port rule  | Open/close specific port                       |
| Rich rules | Advanced filtering with src/dest/port/protocol |
| NAT        | Translation for routing private → public IP    |

---

If you want, I can also provide:

◆ A **one-page Firewall cheat sheet**
◆ A **step-by-step firewall lab workbook**
◆ A **diagram-based explanation of NAT (SNAT/DNAT)**

Which one would you like next?
