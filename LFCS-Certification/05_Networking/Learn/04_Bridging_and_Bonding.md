
# 🟦 **1. Network Bridge (br0)**

A **network bridge** works like a **virtual network switch**.
It connects multiple interfaces at **Layer-2 (Ethernet level)** so they behave as if they are on the same physical network.

---

# 📌 **Why Bridges Are Used?**

### ✔ Virtualization (KVM, QEMU)

Virtual machines need access to the LAN network.
Bridging allows VMs to share the host NIC.

### ✔ Containers (Docker, Podman)

Creates bridges like `docker0`.

### ✔ Connecting multiple interfaces into one LAN segment.

---

# 🟨 **Simple Diagram of a Bridge**

```
                     +---------------------+
Physical NIC ens33 → |       br0          | → VM1 eth0
                     |  (Acts like switch) | → VM2 eth0
                     +---------------------+
```

`br0` controls the IP, not `ens33`.

---

# 🔍 **How Bridge Works**

* `ens33` (slave) **forwards packets** to `br0`.
* `br0` becomes the interface with the IP address.
* Other interfaces or VMs connect to `br0` internally.

---

# 🛠 Example: Configure a Bridge with `nmcli`

### Step 1: Create bridge `br0`

```bash
nmcli connection add type bridge con-name br0 ifname br0
```

### Step 2: Add physical NIC as a slave

```bash
nmcli connection add type bridge-slave con-name br0-port1 \
  ifname ens33 master br0
```

### Step 3: Assign static IP to the bridge

```bash
nmcli connection modify br0 ipv4.addresses 192.168.1.50/24 \
  ipv4.gateway 192.168.1.1 ipv4.method manual
```

### Step 4: Bring up the connection

```bash
nmcli connection up br0
```

Now:

* **br0** = Has IP
* **ens33** = Only forwards packets

---

# 🔹 When to Use a Bridge?

| Use Case                       | Example                      |
| ------------------------------ | ---------------------------- |
| Virtualization                 | KVM, VirtualBox, QEMU        |
| Containers                     | Docker bridge networking     |
| Need Ethernet-level forwarding | Multiple NICs on same subnet |

---

# 🟦 Summary: Bridge

* Acts like a **switch**
* Used mainly in **virtualization**
* IP assigned to the **bridge**, not the physical NIC
* Physical NIC(s) become **bridge ports/slaves**

---

---

# 🟧 **2. Network Bonding (bond0)**

Network Bonding (aka NIC teaming) combines **two or more physical NICs** into **one logical interface**.

Purpose:

### ✔ High Availability (Failover)

If one NIC fails, traffic moves to the other.

### ✔ Load Balancing (Performance)

Distribute traffic across multiple NICs.

---

# 📌 **Bonding Modes (Most Important)**

| Mode  | Name           | Purpose                           |
| ----- | -------------- | --------------------------------- |
| **0** | balance-rr     | Load balancing (round-robin)      |
| **1** | active-backup  | Failover (HA)                     |
| **2** | balance-xor    | Load balance XOR hashing          |
| **4** | 802.3ad (LACP) | Requires switch support           |
| **5** | balance-tlb    | Adaptive load balance             |
| **6** | balance-alb    | Adaptive load + receive balancing |

### Most used:

* **mode 1 (active-backup)** — for servers, no switch config required
* **mode 4 (LACP)** — enterprise switches, high throughput

---

# 🟩 Bonding Diagram (active-backup mode)

```
          +------------------+
NIC1 →    |                  |
          |      bond0       | → OS sees only *one* interface
NIC2 →    | (active-backup)  |
          +------------------+
              | Active NIC chosen
```

If NIC1 dies → NIC2 takes over instantly.

---

# 🛠 Example: Create a Bond with `nmcli`

### Step 1: Create bond interface

```bash
nmcli connection add type bond ifname bond0 con-name bond0 mode active-backup
```

### Step 2: Add slave NICs to bond

```bash
nmcli connection add type bond-slave ifname ens33 master bond0
nmcli connection add type bond-slave ifname ens34 master bond0
```

### Step 3: Assign IP to bond0

```bash
nmcli connection modify bond0 ipv4.addresses 192.168.1.100/24 \
  ipv4.gateway 192.168.1.1 ipv4.method manual
```

### Step 4: Activate

```bash
nmcli connection up bond0
```

---

# 🔎 Verify Bond Status

```bash
cat /proc/net/bonding/bond0
```

You will see:

* Active Interface
* Slave interfaces
* Bonding mode
* Link status

---

# 🔹 When to Use Bonding?

| Use Case                        | Recommended Mode                |
| ------------------------------- | ------------------------------- |
| Failover / High availability    | mode **1** (active-backup)      |
| High throughput / LACP switches | mode **4** (802.3ad)            |
| Simple load balancing           | mode **0**, **2**, **5**, **6** |

---

# 🟧 Summary: Bonding

* Combines **multiple NICs**
* Creates one logical interface (`bond0`)
* Improves **redundancy** and/or **performance**
* IP is assigned to **bond0**, not to slave NICs
* Slaves only forward packets

---

# 🟦 Bridge vs Bonding — Quick Comparison

| Feature                | Bridge                                        | Bonding                                 |
| ---------------------- | --------------------------------------------- | --------------------------------------- |
| Purpose                | Connect multiple interfaces or VMs at Layer 2 | Combine NICs for redundancy/performance |
| Works At               | Layer 2                                       | Layer 2/3                               |
| IP Assigned To         | Bridge (`br0`)                                | Bond (`bond0`)                          |
| Physical NIC Role      | Becomes slave port                            | Becomes slave NIC                       |
| Use Case               | Virtualization, containers                    | Servers, HA, throughput                 |
| Requires Switch Config | No (except advanced setups)                   | Yes (for LACP mode 4)                   |

---

# 🎯 RHCSA / LFCS Exam Notes

You MUST know:

### ✔ Bridge creation:

```
nmcli con add type bridge ...
nmcli con add type bridge-slave ...
```

### ✔ Bond creation:

```
nmcli con add type bond ...
nmcli con add type bond-slave ...
```

### ✔ Where to check bond status:

```
cat /proc/net/bonding/bond0
```

---

# Want next?

I can provide:

✅ One-page Bridge + Bonding cheat sheet
✅ Hands-on lab exercises
✅ Network diagrams showing traffic flow
✅ VLAN + Bonding + Bridge combined examples

Just tell me!
