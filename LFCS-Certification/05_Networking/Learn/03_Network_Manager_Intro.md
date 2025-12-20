Below is a **complete, structured, deep-dive explanation of NetworkManager in RHEL Linux**, with **concepts + commands + examples + diagrams + LFCS/RHCSA-oriented notes**.
This is written cleanly so you can save it into your Linux learning repository.

---

# 🖧 **NetworkManager in RHEL — Full Explanation with Examples**

**NetworkManager (NM)** is the default network configuration and management tool in **RHEL**, **CentOS**, **Rocky Linux**, and **Fedora**.
It is a service (`NetworkManager.service`) that manages:

* Network interfaces (Ethernet, Wi-Fi, VLAN, Bonding, Bridge, VLAN)
* IP addressing (static/DHCP)
* Routing
* DNS
* VPN connections
* Activation/deactivation of interfaces

It comes with:

### ✔ CLI tools

* `nmcli` → Primary command-line interface
* `nmtui` → Text-based UI
* `nm-online` → Check network readiness

### ✔ Configuration files

* `/etc/NetworkManager/`
* `/etc/sysconfig/network-scripts/ifcfg-*` (RHEL 7/8 legacy compatibility)

---

# 🔧 1. NetworkManager Service

Check if NM is running:

```bash
systemctl status NetworkManager
```

Start/Stop:

```bash
sudo systemctl start NetworkManager
sudo systemctl stop NetworkManager
```

Enable on boot:

```bash
sudo systemctl enable NetworkManager
```

---

# 🔌 2. Network Interfaces in NetworkManager

List interfaces:

```bash
nmcli device
```

Example:

```
DEVICE     TYPE      STATE      CONNECTION
ens33      ethernet  connected  ens33
lo         loopback  unmanaged  --
docker0    bridge    unmanaged  --
```

Meaning:

| STATE        | Meaning                             |
| ------------ | ----------------------------------- |
| connected    | Device is active and has connection |
| disconnected | Not connected                       |
| unmanaged    | NM is not controlling it            |

To allow NM to manage an interface:

```bash
sudo nmcli device set docker0 managed yes
```

---

# 🧠 3. NetworkManager Concepts

---

## 3.1 **Device vs Connection**

NM separates **device** and **connection profile**.

### ✔ Device

Physical or virtual network interface
Example: `ens33`, `lo`, `wlan0`

### ✔ Connection

A configuration profile applied to a device
Example: `ens33`, `Wired connection 1`

A single device can have **multiple connection profiles** (e.g., DHCP profile, static profile).

List available connections:

```bash
nmcli connection show
```

---

## 3.2 **Connection Types**

Most common:

* `ethernet`
* `wifi`
* `bridge`
* `bond`
* `vlan`
* `team`
* `vpn`

Example:

```bash
nmcli connection show ens33
```

---

# 📡 4. Viewing Network Details

### Show full device details:

```bash
nmcli device show ens33
```

Shows:

* IP4 address
* Gateway
* DNS
* MTU
* MAC Address

### Show active connection configuration:

```bash
nmcli connection show ens33
```

---

# 🛠 5. Managing IP Addressing

---

## 5.1 **Set Static IP**

Command format:

```bash
nmcli con mod <connection-name> <setting>
```

Example: Set static IP on `ens33`:

```bash
sudo nmcli connection modify ens33 ipv4.addresses 192.168.1.50/24
sudo nmcli connection modify ens33 ipv4.gateway 192.168.1.1
sudo nmcli connection modify ens33 ipv4.dns "8.8.8.8 1.1.1.1"
sudo nmcli connection modify ens33 ipv4.method manual
```

Apply changes:

```bash
sudo nmcli connection up ens33
```

---

## 5.2 **Switch to DHCP**

```bash
sudo nmcli connection modify ens33 ipv4.method auto
sudo nmcli connection up ens33
```

---

## 5.3 **Add Secondary IP Address**

```bash
sudo nmcli con mod ens33 +ipv4.addresses 192.168.1.60/24
```

---

## 5.4 **Set Static DNS Only**

```bash
sudo nmcli con mod ens33 ipv4.dns "8.8.8.8"
```

---

# 🔀 6. Routing Management

### Show routes:

```bash
nmcli device show ens33 | grep ROUTE
```

### Add static route:

```bash
nmcli con mod ens33 +ipv4.routes "10.10.20.0/24 192.168.1.1"
```

Apply:

```bash
nmcli con up ens33
```

---

# 📶 7. VLAN Configuration

Create VLAN:

```bash
sudo nmcli connection add type vlan con-name vlan10 dev ens33 id 10 ip4 192.168.10.10/24
```

Bring it up:

```bash
nmcli con up vlan10
```

---

# 🔗 8. Bonding (NIC Teaming)

### Create bonding interface:

```bash
sudo nmcli con add type bond ifname bond0 mode active-backup
```

### Add slave interfaces:

```bash
sudo nmcli con add type bond-slave ifname ens33 master bond0
sudo nmcli con add type bond-slave ifname ens34 master bond0
```

Assign IP:

```bash
sudo nmcli con mod bond0 ipv4.addresses 192.168.1.100/24 ipv4.method manual
```

Activate:

```bash
nmcli con up bond0
```

---

# 🌉 9. Bridge Interface (Used in KVM)

Create bridge:

```bash
sudo nmcli con add type bridge con-name br0 ifname br0
```

Add physical NIC to bridge:

```bash
sudo nmcli con add type bridge-slave ifname ens33 master br0
```

Assign IP to bridge:

```bash
sudo nmcli con mod br0 ipv4.addresses 192.168.1.30/24 ipv4.method manual
```

---

# 📡 10. Wi-Fi Management (RHEL Laptop/Server with WiFi)

Scan Networks:

```bash
nmcli dev wifi list
```

Connect:

```bash
nmcli dev wifi connect "MyWiFi" password "12345678"
```

---

# 🧰 11. Text UI Tool — `nmtui`

Launch:

```bash
nmtui
```

It allows:

* Edit connection
* Activate connection
* Set system hostname

Very useful in server environments without GUI.

---

# 🔍 12. Debugging & Logs

### Restart NM:

```bash
sudo systemctl restart NetworkManager
```

### View logs:

```bash
journalctl -u NetworkManager -xe
```

### Check device status:

```bash
nmcli -f GENERAL device show ens33
```

---

# 📄 13. NetworkManager Configuration Files

Location:

```
/etc/NetworkManager/
```

### Connection profiles stored here:

```
/etc/NetworkManager/system-connections/*.nmconnection
```

View a connection file:

```bash
sudo cat /etc/NetworkManager/system-connections/ens33.nmconnection
```

Apply config file changes:

```bash
sudo nmcli connection reload
```

---

# 🔥 14. Essential nmcli Commands Cheat Sheet

| Task              | Command                              |
| ----------------- | ------------------------------------ |
| List devices      | `nmcli device`                       |
| List connections  | `nmcli con show`                     |
| Show a connection | `nmcli con show ens33`               |
| Activate          | `nmcli con up ens33`                 |
| Deactivate        | `nmcli con down ens33`               |
| Modify static IP  | `nmcli con mod ens33 ipv4.addresses` |
| Add route         | `nmcli con mod ens33 +ipv4.routes`   |
| Delete connection | `nmcli con delete ens33`             |
| Create VLAN       | `nmcli con add type vlan ...`        |
| Create bond       | `nmcli con add type bond ...`        |

---

# 📘 15. ASCII Diagram — How NetworkManager Controls Networking

```
+----------------------------+
|       Applications         |
|  (ssh, curl, browser)      |
+-------------+--------------+
              |
              v
+----------------------------+
|        Linux Kernel        |
|   Networking Subsystem     |
+-------------+--------------+
              |
              v
+----------------------------+
|      NetworkManager        |
|  Profiles, DHCP, DNS, IP   |
+-------------+--------------+
              |
              v
+----------------------------+
|   Network Interfaces (NIC) |
|  eth0, ens33, bond0, lo    |
+-------------+--------------+
              |
              v
            Network
```

---

# 📝 16. RHCSA/RHEL Exam-Oriented Practice Questions

### **Q1. Configure a static IP using nmcli.**

→ Expect commands:

```
nmcli con mod ens33 ipv4.addresses ...
nmcli con mod ens33 ipv4.gateway ...
nmcli con mod ens33 ipv4.method manual
nmcli con up ens33
```

---

### **Q2. Create VLAN interface `eth0.50` with IP `192.168.50.10/24`.**

```
nmcli con add type vlan con-name vlan50 dev eth0 id 50 ip4 192.168.50.10/24
nmcli con up vlan50
```

---

### **Q3. Create a bonded interface `bond0` using `active-backup` mode.**

```
nmcli con add type bond ifname bond0 mode active-backup
nmcli con add type bond-slave ifname ens33 master bond0
nmcli con add type bond-slave ifname ens34 master bond0
```

---

### **Q4. Use nmtui to set hostname and configure network.**

Exam expects you to know:

```
nmtui
```

---

# 🎉 Summary

NetworkManager is the **central network management system in RHEL**, providing:

* Device management
* IP configuration (static/DHCP)
* Routes & DNS
* VLAN, bonding, bridging
* Profiles
* CLI (`nmcli`), TUI (`nmtui`)

Mastering it is essential for **RHEL administration**, **RHCSA exam**, **production server management**.

---

If you'd like, I can create:

✅ A **one-page NetworkManager cheat sheet**
✅ A **GitHub-ready Markdown file**
✅ **Hands-on lab exercises** based on RHCSA

Which one do you want next?



---
---

# Summary:

Here is a **clean, concise, one-page NetworkManager Cheat Sheet** — perfect for quick revision or GitHub notes.

---

# 🖧 **NetworkManager Cheat Sheet (RHEL/CentOS/Rocky Linux)**

## 🔹 Service Management

```bash
systemctl status NetworkManager
systemctl restart NetworkManager
systemctl enable NetworkManager
```

---

# 🔹 Device & Connection Basics

### List devices

```bash
nmcli device
```

### List all connection profiles

```bash
nmcli con show
```

### Show device details

```bash
nmcli device show ens33
```

### Show connection configuration

```bash
nmcli con show ens33
```

---

# 🔹 Activate / Deactivate Connections

```bash
nmcli con up ens33
nmcli con down ens33
```

---

# 🔹 Set Static IP

```bash
nmcli con mod ens33 ipv4.addresses 192.168.1.50/24
nmcli con mod ens33 ipv4.gateway 192.168.1.1
nmcli con mod ens33 ipv4.dns "8.8.8.8 1.1.1.1"
nmcli con mod ens33 ipv4.method manual
nmcli con up ens33
```

# 🔹 Set DHCP

```bash
nmcli con mod ens33 ipv4.method auto
nmcli con up ens33
```

---

# 🔹 Add Secondary IP

```bash
nmcli con mod ens33 +ipv4.addresses 192.168.1.60/24
```

---

# 🔹 Add Static Route

```bash
nmcli con mod ens33 +ipv4.routes "10.10.20.0/24 192.168.1.1"
nmcli con up ens33
```

---

# 🔹 DNS Settings

```bash
nmcli con mod ens33 ipv4.dns "8.8.8.8"
nmcli con mod ens33 ipv4.ignore-auto-dns yes
```

---

# 🔹 VLAN Configuration

```bash
nmcli con add type vlan con-name vlan10 dev ens33 id 10 ip4 192.168.10.10/24
nmcli con up vlan10
```

---

# 🔹 Bonding (NIC Teaming)

```bash
nmcli con add type bond ifname bond0 mode active-backup
nmcli con add type bond-slave ifname ens33 master bond0
nmcli con add type bond-slave ifname ens34 master bond0
nmcli con mod bond0 ipv4.addresses 192.168.1.100/24 ipv4.method manual
nmcli con up bond0
```

---

# 🔹 Bridge Configuration (for KVM)

```bash
nmcli con add type bridge con-name br0 ifname br0
nmcli con add type bridge-slave ifname ens33 master br0
nmcli con mod br0 ipv4.addresses 192.168.1.30/24 ipv4.method manual
nmcli con up br0
```

---

# 🔹 Wi-Fi (Laptop/Server with WLAN)

Scan:

```bash
nmcli dev wifi list
```

Connect:

```bash
nmcli dev wifi connect "SSID" password "PASSWORD"
```

---

# 🔹 Text UI (TUI)

```bash
nmtui
```

Allows:

* Edit connection
* Activate connection
* Set hostname

---

# 🔹 Troubleshooting

Logs:

```bash
journalctl -u NetworkManager -xe
```

Check readiness:

```bash
nm-online
```

Restart NIC:

```bash
nmcli dev disconnect ens33
nmcli dev connect ens33
```

---

# 🔹 Connection File Locations (Important)

```
/etc/NetworkManager/system-connections/*.nmconnection
```

Reload after editing:

```bash
nmcli connection reload
```

---

# 🎯 **Perfect For RHCSA Exam**

Know how to configure:

* Static IP
* Route
* DNS
* VLAN
* Bond
* Bridge
* Wi-Fi
* `nmtui`

This cheat sheet covers all required commands.

---

If you'd like, I can turn this into a **printable PDF**, or create a **NetworkManager hands-on lab + practice tasks** like in RHCSA exam.
