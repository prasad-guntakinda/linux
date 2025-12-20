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


<details>
<summary>Does `nmcli` changes are permanent?</summary>


Yes — **IP address changes made using `nmcli` are permanent**, *as long as you update the connection profile* (which `nmcli` does by default when modifying a connection).

---

# ✅ **Why nmcli changes are permanent**

`nmcli` modifies the **connection profile**, not just the live interface state.

A connection profile is a persistent configuration stored under:

```
/etc/NetworkManager/system-connections/
```

Each profile is a file ending with:

```
.nmconnection
```

Example:

```
/etc/NetworkManager/system-connections/ens33.nmconnection
```

Inside this file, you will find:

* IP address
* Gateway
* DNS
* Routes
* Autoconnect settings
* VLAN/bond options

### Example snippet:

```
[ipv4]
address1=192.168.1.50/24,192.168.1.1
dns=8.8.8.8;
method=manual
```

When NetworkManager restarts or the system reboots, it re-applies values from this file.

---

# 📌 **Where changes are saved**

### ⭐ **Location:**

```
/etc/NetworkManager/system-connections/<connection-name>.nmconnection
```

Example:

```
/etc/NetworkManager/system-connections/ens33.nmconnection
```

Permissions are usually:

```
-rw-------  root root
```

---

# 📌 **Confirm the saved configuration**

You can check persistent settings using:

```bash
nmcli connection show ens33
```

Or view the actual file:

```bash
sudo cat /etc/NetworkManager/system-connections/ens33.nmconnection
```

---

# 📝 When nmcli changes are NOT permanent

Changes are **not permanent** only when you modify the live device instead of the connection.

Example (NOT persistent):

```bash
nmcli device modify ens33 ipv4.addresses 192.168.1.50/24
```

This affects the live interface but does **not** update the `.nmconnection` file.

Correct way (persistent):

```bash
nmcli connection modify ens33 ipv4.addresses 192.168.1.50/24
```

---

# 🎯 Summary (Very Important)

| Action                        | Permanent? | Why                                           |
| ----------------------------- | ---------- | --------------------------------------------- |
| `nmcli connection modify ...` | ✅ Yes      | Updates `.nmconnection` profile               |
| `nmcli connection add ...`    | ✅ Yes      | Creates a new persistent profile              |
| `nmcli connection up/down`    | ⚠️ Yes     | Activates profile but doesn’t change settings |
| `nmcli device modify ...`     | ❌ No       | Only changes runtime values                   |

---


</details>

---

<details>

<summary>Difference between `nmcli device` and `nmcli connection`</summary>

Here is a **clear, crisp, exam-friendly explanation** of the difference between
**`nmcli device`** and **`nmcli connection`**, with examples and a comparison table.

---

# 🔥 **Difference Between `nmcli device` and `nmcli connection`**

NetworkManager works with **two different objects**:

1️⃣ **Devices** → Actual network interfaces (hardware or virtual)
2️⃣ **Connections** → Configuration profiles applied to devices

They are different things.

---

# 🖧 1. **`nmcli device` (Real Hardware Interface)**

`nmcli device` manages **current runtime state** of network interfaces.

Examples of devices:

* `ens33`
* `wlan0`
* `lo`
* `bond0`
* `br0`

### 🔹 What `nmcli device` is used for:

| What you can do               | Command                              |
| ----------------------------- | ------------------------------------ |
| List devices                  | `nmcli device`                       |
| Show device details           | `nmcli device show ens33`            |
| Connect a device to a profile | `nmcli device connect ens33`         |
| Disconnect a device           | `nmcli device disconnect ens33`      |
| Set managed/unmanaged         | `nmcli device set ens33 managed yes` |

### 🚫 Important:

`nmcli device` **does NOT modify permanent configuration**.

It only affects the **live state** of the interface.

---

# 📁 2. **`nmcli connection` (Persistent Configuration Profiles)**

A **connection** is a saved configuration file used by NetworkManager.

Examples of connections:

* `Wired connection 1`
* `ens33`
* `Home WiFi`
* `vlan10`
* `bond0`

Connection profiles are stored here:

```
/etc/NetworkManager/system-connections/*.nmconnection
```

### 🔹 What `nmcli connection` does:

| Action                    | Example                                              |
| ------------------------- | ---------------------------------------------------- |
| Create connection         | `nmcli con add ...`                                  |
| Modify persistent IP, DNS | `nmcli con mod ens33 ipv4.addresses 192.168.1.50/24` |
| Bring connection up       | `nmcli con up ens33`                                 |
| Delete connection         | `nmcli con delete ens33`                             |
| Show connection settings  | `nmcli con show ens33`                               |

### ✔ Important:

`nmcli connection` **writes permanent settings** to `.nmconnection` files.

---

# 🧠 **Big Concept: Device vs Connection**

### 🔸 A **device** is a network interface (hardware)

### 🔸 A **connection** is a configuration profile

A device can have **multiple connection profiles**, but **only one can be active at a time**.

---

# 🔍 Example to Understand the Difference

## Example device list:

```
nmcli device
```

Output:

```
DEVICE    TYPE      STATE        CONNECTION
ens33     ethernet  connected    ens33
wlan0     wifi      disconnected --
```

* `ens33` is a **device**
* `ens33` is also the **name of a connection profile**

But they are not the same thing.

---

## Example: Modify IP Address

❌ Wrong (not persistent):

```bash
nmcli device modify ens33 ipv4.addresses 10.1.1.50/24
```

(This will not survive reboot.)

✔ Correct (persistent):

```bash
nmcli connection modify ens33 ipv4.addresses 10.1.1.50/24
nmcli connection up ens33
```

---

# 📊 Side-by-Side Comparison

| Feature                 | `nmcli device`            | `nmcli connection`                 |
| ----------------------- | ------------------------- | ---------------------------------- |
| Works on                | Actual NIC                | Configuration profile              |
| Persistence             | ❌ Runtime only            | ✅ Permanent                        |
| File created/edited     | No                        | Yes (`*.nmconnection`)             |
| Modify IP, DNS          | No                        | Yes                                |
| Bring interface up/down | Yes                       | Yes                                |
| Create VLAN/Bond/Bridge | No                        | Yes                                |
| For diagnostics         | Yes                       | No                                 |
| For configuration       | No                        | Yes                                |
| Example use             | `device disconnect ens33` | `con mod ens33 ipv4.method manual` |

---

# 🎯 Quick Summary (Exam-type)

### **`nmcli device` → controls physical interfaces (runtime state)**

### **`nmcli connection` → manages persistent configuration profiles**

---

If you want, I can also provide:

✔ Real-world scenarios (e.g., SSH-safe changes)
✔ RHCSA-style questions on this topic
✔ A diagram comparing device vs connection

Just tell me!


</details>


---

<details>
<summary> How to create a NetworkManager connection profile file? </summary>

Below is a **clear, step-by-step explanation** of:

1️⃣ *How to create a NetworkManager connection profile*
2️⃣ *How to attach (apply) that profile to a specific device*

This is RHCSA-ready and production-safe.

---

# 🟦 **1. How NetworkManager Profiles Work**

A **connection profile** is a persistent configuration stored here:

```
/etc/NetworkManager/system-connections/<name>.nmconnection
```

You can create a profile *without* applying it immediately, or you can create and activate it in one step.

A profile contains:

* Interface name
* IP address (static or DHCP)
* Gateway
* DNS
* MTU, routes, VLANs, bond members, etc.

---

# 🟩 **2. Create a Connection Profile (Static IP Example)**

### ➤ Create profile for interface `ens33` with static IP:

```bash
sudo nmcli connection add type ethernet \
       con-name static-ens33 \
       ifname ens33 \
       ipv4.addresses 192.168.1.50/24 \
       ipv4.gateway 192.168.1.1 \
       ipv4.dns "8.8.8.8" \
       ipv4.method manual \
       autoconnect yes
```

### Breakdown:

* `type ethernet` → it's a wired connection
* `con-name static-ens33` → profile name
* `ifname ens33` → apply to device
* `ipv4.method manual` → static IP
* `autoconnect yes` → activate on boot

### ✔ This creates a persistent profile file:

```
/etc/NetworkManager/system-connections/static-ens33.nmconnection
```

---

# 🟨 **3. Activate the Profile (Apply it to the Device)**

```bash
sudo nmcli connection up static-ens33
```

This makes the profile active on the device (`ens33`).

### Check:

```bash
nmcli device status
```

You should see:

```
DEVICE   TYPE      STATE      CONNECTION
ens33    ethernet  connected  static-ens33
```

---

# 🟧 **4. Create a DHCP Connection Profile**

```bash
sudo nmcli connection add type ethernet \
       con-name dhcp-ens33 \
       ifname ens33 \
       ipv4.method auto \
       autoconnect yes
```

Activate it:

```bash
nmcli connection up dhcp-ens33
```

Now `ens33` will get an IP via DHCP.

---

# 🟥 **5. How to Assign an Existing Profile to a Device**

Sometimes you create a profile *without* binding it to a device, or you want to switch profiles.

### Example profile with no device:

```
con-name office-net
```

### Attach it to a device:

```bash
sudo nmcli connection modify office-net ifname ens33
```

### Activate it:

```bash
sudo nmcli connection up office-net
```

---

# 🟦 **6. Switch Between Profiles on the Same Device**

Example: two profiles:

```
static-ens33
dhcp-ens33
```

### Switch to static:

```bash
nmcli connection up static-ens33
```

### Switch to DHCP:

```bash
nmcli connection up dhcp-ens33
```

NetworkManager will automatically deactivate the previous one.

---

# 🟫 **7. Create a Profile Without Assigning to a Device**

Useful for templates or reusable profiles.

```bash
sudo nmcli connection add type ethernet con-name template0 autoconnect no
```

Assign later:

```bash
sudo nmcli con mod template0 ifname ens33
```

---

# 🟪 **8. View Connection Profiles and Their Devices**

### List profiles:

```bash
nmcli connection show
```

### List devices:

```bash
nmcli device
```

### Check which profile is active on a device:

```bash
nmcli -f DEVICE,STATE,CONNECTION device status
```

---

# 🟫 **9. Show Profile File (Verify Storage)**

```bash
sudo cat /etc/NetworkManager/system-connections/static-ens33.nmconnection
```

You will see:

```
[ipv4]
address1=192.168.1.50/24,192.168.1.1
dns=8.8.8.8;
method=manual
```

This confirms **profile is persistent**.

---

# 🟰 **10. Summary — Simple and Visual**

```
             +--------------------------+
             | Create a connection      |
             | nmcli connection add     |
             +-------------+------------+
                           |
                           v
             +--------------------------+
             | (Optional) Modify config |
             | nmcli connection modify  |
             +-------------+------------+
                           |
                           v
             +--------------------------+
             | Assign to a device       |
             | ifname ens33             |
             +-------------+------------+
                           |
                           v
             +--------------------------+
             | Activate profile         |
             | nmcli connection up      |
             +--------------------------+
```

---

# ✅ Want more?

I can also provide:

* Hands-on lab tasks for RHCSA
* A cheat sheet for creating VLAN, BRIDGE, BOND profiles
* A visual diagram comparing `device` vs `connection`

Just tell me!

</details>


---

<details>
<summary> How to create dummy network bridging and bonding ?
</summary>


Yes — **you absolutely CAN create your own network interfaces and NICs locally** to practice **bridge**, **bonding**, **VLAN**, **routing**, etc.

You do *not* need real hardware with multiple NICs.
Linux lets you **simulate NICs virtually** for lab practice.

Below is a **complete step-by-step guide** that works on:

* RHEL / CentOS / Rocky / Alma
* Fedora
* Ubuntu (with small differences)
* KVM servers or ANY Linux machine

---

# 🟦 **1. YES — You Can Create Your Own Network Interfaces**

Linux supports multiple types of **virtual NICs**:

| Interface Type | Example            | Used for                       |
| -------------- | ------------------ | ------------------------------ |
| **Dummy NIC**  | `dummy0`, `dummy1` | Practice bonding, IPs, routing |
| **Veth pair**  | `veth0`, `veth1`   | Container-like interfaces      |
| **Bridge**     | `br0`              | Virtualization                 |
| **Bond**       | `bond0`            | NIC teaming practice           |
| **VLAN NIC**   | `eth0.10`          | VLAN practice                  |
| **Tap/Tun**    | `tun0`, `tap0`     | VPN training                   |

You can create all of these *without* any real hardware.

---

# 🟧 **2. Best Method for Local Practice: Create Dummy NICs**

Dummy NICs behave like real physical NICs.

Enable `dummy` module:

```bash
sudo modprobe dummy
```

Create dummy interface:

```bash
sudo ip link add dummy0 type dummy
sudo ip link set dummy0 up
```

Add a second dummy NIC:

```bash
sudo ip link add dummy1 type dummy
sudo ip link set dummy1 up
```

Check:

```bash
ip a
```

You will see:

```
dummy0: <BROADCAST,UP> ...
dummy1: <BROADCAST,UP> ...
```

Now you have **two NICs** to practice bonding or bridges.

---

# 🟩 **3. Practice Network Bonding Using Dummy NICs**

### Step 1: Create bond0

```bash
sudo nmcli con add type bond ifname bond0 mode active-backup
```

### Step 2: Add dummy NICs as slaves

```bash
sudo nmcli con add type bond-slave ifname dummy0 master bond0
sudo nmcli con add type bond-slave ifname dummy1 master bond0
```

### Step 3: Assign an IP

```bash
sudo nmcli con mod bond0 ipv4.addresses 10.10.10.10/24 ipv4.method manual
```

Activate:

```bash
sudo nmcli con up bond0
```

Check bond status:

```bash
cat /proc/net/bonding/bond0
```

🎉 **You have a working bond interface using artificial NICs.**

---

# 🟨 **4. Practice Network Bridge Using Dummy NICs**

Step 1: Create bridge:

```bash
sudo nmcli con add type bridge con-name br0 ifname br0
```

Step 2: Add dummy NIC to the bridge:

```bash
sudo nmcli con add type bridge-slave con-name br0-port0 \
  ifname dummy0 master br0
```

Step 3: Assign IP to the bridge:

```bash
sudo nmcli con mod br0 ipv4.addresses 192.168.100.10/24 ipv4.method manual
```

Bring it up:

```bash
sudo nmcli con up br0
```

Test:

```bash
ping -I br0 192.168.100.1
```

🎉 **Bridge created using virtual NIC.**

---

# 🟥 **5. Alternative: Use Virtual Machines (Best Full Lab Setup)**

If you want a full enterprise-style lab:

Use **KVM/libvirt** or **VirtualBox** to create VMs.

Inside a VM:

* Use `dummy` NICs to simulate multiple network cards
* Use bridges (`br0`) for VM networking
* Use bonds (`bond0`) for NIC teaming
* Use VLAN NICs (`eth0.10`) for VLAN testing

### Create a KVM host bridge (optional):

```bash
sudo nmcli con add type bridge ifname br0 con-name br0
sudo nmcli con add type ethernet ifname enp3s0 master br0
```

Attach VMs to `br0` and practice real LAN access.

---

# 🟫 **6. Create Veth Pairs for Advanced Networking Practice**

A veth pair simulates a cable between two NICs.

```bash
sudo ip link add veth0 type veth peer name veth1
sudo ip link set veth0 up
sudo ip link set veth1 up
```

You can:

* Put veth0 inside a bridge
* Assign IPs to both ends
* Test routing / bridging behaviors

---

# 🟪 **7. Practice VLAN Interfaces**

Assume dummy0 is physical NIC.

Create VLAN 10:

```bash
sudo nmcli con add type vlan con-name vlan10 dev dummy0 id 10 \
  ip4 172.16.10.2/24
nmcli con up vlan10
```

Check:

```bash
ip -d link show vlan10
```

---

# 🟦 **8. Persistence for Dummy NICs (Optional)**

To auto-load dummy NICs at boot:

Edit:

```
/etc/modules-load.d/dummy.conf
```

Add:

```
dummy
```

Then in:

```
/etc/modprobe.d/dummy.conf
```

Add:

```
options dummy numdummies=2
```

You will get:

* dummy0
* dummy1

---

# 🟩 **9. Recommended Lab Setup (Professional-Level)**

| Component       | Purpose                            |
| --------------- | ---------------------------------- |
| Dummy NICs      | Practice bonding, bridging, VLANs  |
| Veth pairs      | Simulate traffic paths             |
| Bridges         | Virtualization-like practice       |
| Bonding         | High availability + load balancing |
| VMs             | Real networking tests              |
| Firewalld rules | Practical firewall management      |
| Routing tables  | Dynamic routing practice           |

---

# 🎯 **10. Most Important Commands for Practice**

### Create dummy NIC

```bash
ip link add dummy0 type dummy
```

### Create bond

```bash
nmcli con add type bond ifname bond0 mode active-backup
```

### Create bridge

```bash
nmcli con add type bridge ifname br0 con-name br0
```

### Add slaves

```bash
nmcli con add type bridge-slave ifname dummy0 master br0
```

### Add VLAN

```bash
nmcli con add type vlan dev dummy0 id 20 con-name vlan20
```

---

# 🎉 Final Summary

Yes — you **CAN** create your own NICs locally.

To practice bonding & bridging:

### ✔ Create dummy NICs → `dummy0`, `dummy1`

### ✔ Use nmcli to bond them → `bond0`

### ✔ Use nmcli to bridge them → `br0`

### ✔ Build VLANs → `dummy0.10`

### ✔ Build routing labs → veth pairs

### ✔ Optional → Build full virtualization labs via KVM

This is exactly how Linux training labs simulate enterprise networking.

---


# Cheatsheet:

Here is a **clean, crisp, single-page cheat sheet** for **Network Bridge (br0)** and **Bonding (bond0)** practice — using **nmcli**, dummy NICs, and real NICs.
Perfect for RHCSA, LFCS, and real sysadmin labs.

---

# 🖧 **Network Bridge + Bonding — One-Page Practice Cheat Sheet**

---

# 🟦 **1. Create Dummy NICs (for lab practice)**

Use dummy NICs when you don't have real extra NICs.

```bash
sudo modprobe dummy
sudo ip link add dummy0 type dummy
sudo ip link add dummy1 type dummy
sudo ip link set dummy0 up
sudo ip link set dummy1 up
```

Verify:

```bash
ip -br a
```

---

# 🟩 **2. Create a Bridge (br0)**

Bridge behaves like a virtual switch. IP is assigned to **br0**, not slave NICs.

### ✔ Create bridge

```bash
nmcli con add type bridge con-name br0 ifname br0
```

### ✔ Add NIC to bridge

```bash
nmcli con add type bridge-slave con-name br0-port0 \
       ifname dummy0 master br0
```

### ✔ Add optional second NIC

```bash
nmcli con add type bridge-slave con-name br0-port1 \
       ifname dummy1 master br0
```

### ✔ Assign IP to bridge

```bash
nmcli con mod br0 ipv4.addresses 192.168.100.10/24 \
     ipv4.gateway 192.168.100.1 ipv4.method manual
```

### ✔ Bring up bridge

```bash
nmcli con up br0
```

### ✔ Verify

```bash
bridge link
nmcli device status
```

---

# 🟧 **3. Create a Bond (bond0)**

Bond = Combine NICs for high availability or load balancing.

### ✔ Create bond (active-backup)

```bash
nmcli con add type bond ifname bond0 con-name bond0 mode active-backup
```

### ✔ Add slave NICs

```bash
nmcli con add type bond-slave ifname dummy0 master bond0
nmcli con add type bond-slave ifname dummy1 master bond0
```

### ✔ Assign IP to bond

```bash
nmcli con mod bond0 ipv4.addresses 10.10.10.10/24 \
     ipv4.gateway 10.10.10.1 ipv4.method manual
```

### ✔ Bring up bond

```bash
nmcli con up bond0
```

### ✔ Verify bond details

```bash
cat /proc/net/bonding/bond0
nmcli device
```

---

# 🟨 **4. Switch Bonding Modes (important for exams)**

```bash
nmcli con mod bond0 bond.options "mode=1"         # active-backup
nmcli con mod bond0 bond.options "mode=0"         # round-robin
nmcli con mod bond0 bond.options "mode=4"         # LACP (requires switch support)
```

Apply:

```bash
nmcli con up bond0
```

---

# 🟥 **5. Combine Bridge + Bond (common in virtualization)**

Bond two NICs → use bond as bridge interface.

### Step 1: Create bond

```bash
nmcli con add type bond ifname bond0 mode active-backup con-name bond0
nmcli con add type bond-slave ifname dummy0 master bond0
nmcli con add type bond-slave ifname dummy1 master bond0
```

### Step 2: Create bridge

```bash
nmcli con add type bridge ifname br0 con-name br0
```

### Step 3: Add bond to bridge

```bash
nmcli con add type bridge-slave ifname bond0 master br0
```

### Step 4: Assign IP to bridge

```bash
nmcli con mod br0 ipv4.method manual ipv4.addresses 192.168.50.10/24
nmcli con up br0
```

---

# 🟫 **6. Quick Verification Commands**

| Task                    | Command                                                          |
| ----------------------- | ---------------------------------------------------------------- |
| Show bridges            | `bridge link`                                                    |
| Show bonding status     | `cat /proc/net/bonding/bond0`                                    |
| Show active connections | `nmcli con show`                                                 |
| Show devices            | `nmcli device`                                                   |
| Show profile file       | `sudo cat /etc/NetworkManager/system-connections/*.nmconnection` |

---

# 🟪 **7. Important Concepts (Exam-Ready)**

### ✔ Bridge

* Works at Layer 2
* Acts like a virtual switch
* NICs become bridge ports
* IP belongs to **br0**, not ens33/dummy0

### ✔ Bonding

* NIC teaming (redundancy or throughput)
* IP belongs to **bond0**, not slave NICs
* Modes: 1=active-backup, 0=round-robin, 4=LACP

### ✔ Dummy NICs

* Great for practice
* Behave like real NICs

---

# 🎉 **One-Page Summary Diagram**

```
           Bonding (NIC Teaming)
        +--------------------------+
dummy0 →|                          |→ bond0 → br0 → VM/KVM/Host IP
dummy1 →|   bond0 (active-backup)  |
        +--------------------------+

                Bridging
        +---------------------------+
        |       br0 (virtual switch)|
        +------------+--------------+
                     |
                 bond0 or NIC
```

---

If you want, I can also give you:

📌 A **practice lab workbook** (tasks + solutions)
📌 A **packet flow diagram for bridge & bond**
📌 A **troubleshooting cheat sheet**

Just tell me!



</details>

---
