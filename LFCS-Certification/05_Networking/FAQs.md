# FAQs

<details>
<summary> What is network interface? </summary>


A **network interface in Linux** is any software or hardware component that allows the system to **send and receive data over a network**.
It is the connection point between your Linux system and other systems or networks (LAN, WAN, internet).

Think of it as the "network card" the operating system uses to communicate.

---

# ✅ **Types of Network Interfaces in Linux**

Linux supports multiple kinds of network interfaces:

---

## **1️⃣ Physical Network Interfaces (Hardware-Based)**

These represent real network hardware.

Examples:

* `eth0`, `eth1` — old naming convention for Ethernet cards
* `enp0s3`, `ens33`, `eno1` — new "predictable" naming (RHEL/CentOS/Rocky)
* `wlan0`, `wlp3s0` — Wi-Fi adapters

These correspond to actual network ports on the machine.

---

## **2️⃣ Virtual Network Interfaces**

Linux creates various virtual interfaces used for internal communication, containers, virtualization, networking features.

Examples:

### **🟦 Loopback Interface**

* Name: `lo`
* IP: `127.0.0.1`
* Purpose: system talks to itself
* Always up

### **🟩 Bridge Interfaces**

* Names: `br0`, `virbr0`
* Used for virtualization, LXD, KVM, Docker networks

### **🟧 Bond Interfaces**

* Name: `bond0`
* Combines multiple NICs for redundancy or speed

### **🟨 VLAN Interfaces**

* Names: `eth0.100`, `ens33.200`
* Adds VLAN tagging support

### **🟥 Tunnel Interfaces**

* Names: `tun0`, `tap0`
* Used by VPNs like OpenVPN

---

# ✅ **How to List Network Interfaces**

### **Using `ip` command (modern way):**

```bash
ip a
```

or:

```bash
ip link show
```

### Example output:

```
1: lo: <LOOPBACK,UP> mtu 65536
2: ens33: <BROADCAST,MULTICAST,UP> mtu 1500
3: virbr0: <NO-CARRIER,BROADCAST> mtu 1500
```

---

# ✅ What Information Does a Network Interface Provide?

Each interface has properties such as:

* **Name**: `ens33`, `lo`, `bond0`
* **MAC address**: physical hardware address
* **IP address**: IPv4 or IPv6
* **MTU**: maximum transfer size
* **State**: UP/DOWN
* **Flags**: e.g., BROADCAST, MULTICAST
* **Packets & errors**

---

# ✅ Bringing Interface UP or DOWN

### Bring interface up:

```bash
ip link set ens33 up
```

### Bring interface down:

```bash
ip link set ens33 down
```

---

# 🔥 **Real-World Example: Editing Interface Settings (RHEL)**

Set static IP using NetworkManager:

```bash
nmcli con mod ens33 ipv4.addresses 192.168.1.50/24
nmcli con mod ens33 ipv4.gateway 192.168.1.1
nmcli con mod ens33 ipv4.method manual
nmcli con up ens33
```

---

# 📌 Summary (Easy Definition)

> A **network interface in Linux** is the component—hardware or virtual—that connects your system to a network and allows data communication.
> You interact with it using tools like `ip`, `nmcli`, and configuration files to assign IP addresses, routes, DNS, etc.

---



</details>

---