# Networking Concepts


Below is a **beginner-friendly, deeply explained, LFCS-focused set of networking learning notes** for **RHEL-based systems (RHEL/Rocky/AlmaLinux)**.

---

# LFCS Networking Learning Notes (RHEL-Based Systems)
Beginner-Friendly + Detailed Examples  
Covers: IPv4/IPv6, Hostname Resolution, Time Sync, Troubleshooting, SSH, Firewall, NAT, Routing, Bridging, Bonding, Load Balancing & Reverse Proxy

---

# 📘 1. Configure IPv4 and IPv6 Networking & Hostname Resolution

On RHEL-based systems, networking is managed using:

- **nmcli** (NetworkManager CLI)
- **nmtui** (text UI)
- Files under `/etc/sysconfig/network-scripts/` (legacy but still works)
- DNS settings in `/etc/resolv.conf` or NetworkManager-controlled config

---

## ⭐ 1.1 View Network Interfaces

```bash
ip a
nmcli device status
````

Example output:

```
ens33  connected  ethernet
lo     unmanaged  loopback
```

---

## ⭐ 1.2 Configure IPv4 Static Address (nmcli)

### **Example Requirement**

* Interface: `ens33`
* IP: `192.168.10.50/24`
* Gateway: `192.168.10.1`
* DNS: `8.8.8.8`

### Command:

```bash
nmcli con mod ens33 ipv4.addresses 192.168.10.50/24
nmcli con mod ens33 ipv4.gateway 192.168.10.1
nmcli con mod ens33 ipv4.dns "8.8.8.8"
nmcli con mod ens33 ipv4.method manual
nmcli con up ens33
```

Check:

```bash
ip a show ens33
```

---

## ⭐ 1.3 Configure DHCP (IPv4)

```bash
nmcli con mod ens33 ipv4.method auto
nmcli con up ens33
```

---

## ⭐ 1.4 Configure IPv6 Static Address

Example:

* IPv6: `2001:db8:abcd:1::10/64`
* Gateway: `2001:db8:abcd:1::1`

```bash
nmcli con mod ens33 ipv6.addresses "2001:db8:abcd:1::10/64"
nmcli con mod ens33 ipv6.gateway "2001:db8:abcd:1::1"
nmcli con mod ens33 ipv6.method manual
nmcli con up ens33
```

---

## ⭐ 1.5 Configure Hostname

Show hostname:

```bash
hostnamectl
```

Set hostname:

```bash
sudo hostnamectl set-hostname server01.example.com
```

Add local resolution:

```bash
sudo nano /etc/hosts
```

Add entry:

```
192.168.10.50   server01
```

---

# 📘 2. System Time Synchronization (chronyd)

RHEL uses **chronyd**, a modern NTP implementation.

---

## ⭐ 2.1 Install chrony (if missing)

```bash
sudo dnf install chrony -y
sudo systemctl enable --now chronyd
```

---

## ⭐ 2.2 Configure NTP Servers

Edit:

```bash
sudo nano /etc/chrony.conf
```

Add or adjust:

```
server pool.ntp.org iburst
```

Restart service:

```bash
sudo systemctl restart chronyd
```

---

## ⭐ 2.3 Check Time Sync Status

```bash
chronyc tracking
chronyc sources
```

---

## ⭐ 2.4 Force Sync

```bash
sudo chronyc makestep
```

---

# 📘 3. Monitor & Troubleshoot Networking

---

## ⭐ 3.1 Check Connectivity

Ping IPv4:

```bash
ping -c 4 google.com
```

Ping IPv6:

```bash
ping6 -c 4 ipv6.google.com
```

---

## ⭐ 3.2 DNS Troubleshooting

```bash
dig google.com
host google.com
nslookup google.com
```

List DNS resolvers:

```bash
cat /etc/resolv.conf
```

---

## ⭐ 3.3 Check Routing Table

```bash
ip route
ip -6 route
```

---

## ⭐ 3.4 Check Active Connections

```bash
ss -tulnp
```

Meaning:

* `t` = TCP
* `u` = UDP
* `l` = listening sockets
* `n` = numeric format
* `p` = process info

---

## ⭐ 3.5 Check Network Interfaces

```bash
nmcli device show ens33
ip link
```

---

## ⭐ 3.6 Disable/Enable Interface

```bash
nmcli device disconnect ens33
nmcli device connect ens33
```

---

# 📘 4. Configure OpenSSH Server & Client

---

## ⭐ 4.1 Install SSH Server

```bash
sudo dnf install openssh-server -y
sudo systemctl enable --now sshd
```

Check status:

```bash
systemctl status sshd
```

---

## ⭐ 4.2 Configure SSH Server

Edit:

```bash
sudo nano /etc/ssh/sshd_config
```

Common settings:

```
Port 22
PermitRootLogin no
PasswordAuthentication no
AllowUsers admin user1
```

Restart:

```bash
sudo systemctl restart sshd
```

---

## ⭐ 4.3 SSH Client Usage

Login:

```bash
ssh user@example.com
```

Copy files:

```bash
scp file.txt user@server:/tmp/
```

Copy directory:

```bash
scp -r dir/ user@server:/data/
```

Generate SSH keys:

```bash
ssh-keygen -t ed25519
ssh-copy-id user@server
```

---

# 📘 5. Configure Packet Filtering, Port Redirection, and NAT (firewalld + nftables)

RHEL uses **firewalld** with **nftables** backend.

---

## ⭐ 5.1 Basic Firewall Commands

Check status:

```bash
sudo firewall-cmd --state
```

List allowed services:

```bash
sudo firewall-cmd --list-all
```

---

## ⭐ 5.2 Allow Common Services

HTTP:

```bash
firewall-cmd --permanent --add-service=http
```

SSH:

```bash
firewall-cmd --permanent --add-service=ssh
```

Custom port:

```bash
firewall-cmd --permanent --add-port=8080/tcp
```

Reload:

```bash
firewall-cmd --reload
```

---

## ⭐ 5.3 Port Forwarding Example

Forward port 8080 → 80

```bash
firewall-cmd --permanent --add-forward-port=port=8080:proto=tcp:toport=80
firewall-cmd --reload
```

---

## ⭐ 5.4 Configure NAT (Masquerading)

Enable NAT for outbound traffic:

```bash
firewall-cmd --permanent --add-masquerade
firewall-cmd --reload
```

Example NAT rule:
LAN: `192.168.1.0/24`
Outbound interface: `ens33`

Allow NAT:

```bash
firewall-cmd --permanent --zone=public --add-rich-rule='rule family=ipv4 source address=192.168.1.0/24 masquerade'
```

---

# 📘 6. Configure Static Routing

---

## ⭐ 6.1 Add IPv4 Route Temporarily

Route to network `10.20.0.0/24` via gateway `10.10.10.1`:

```bash
ip route add 10.20.0.0/24 via 10.10.10.1
```

---

## ⭐ 6.2 Make a Static Route Persistent

Method: NetworkManager keyfile

```bash
nmcli con mod ens33 +ipv4.routes "10.20.0.0/24 10.10.10.1"
nmcli con up ens33
```

Verify:

```bash
ip route
```

---

## ⭐ 6.3 Delete Route

```bash
ip route del 10.20.0.0/24 via 10.10.10.1
```

---

# 📘 7. Configure Bridge and Bonding Devices

---

## ⭐ 7.1 Network Bridge (Common in VMs & Containers)

Create bridge `br0`:

```bash
nmcli con add type bridge ifname br0 con-name br0
nmcli con mod br0 ipv4.addresses 192.168.1.200/24 ipv4.gateway 192.168.1.1 ipv4.method manual
```

Add a physical interface:

```bash
nmcli con add type bridge-slave ifname ens33 master br0
nmcli con up br0
```

Verify:

```bash
ip link show br0
```

---

## ⭐ 7.2 Bonding (NIC Teaming)

Bond modes:

* `active-backup` (most common)
* `802.3ad` (LACP)
* `balance-rr`

### Example: Create a bond device `bond0`

```bash
nmcli con add type bond ifname bond0 mode active-backup
nmcli con add type bond-slave ifname ens33 master bond0
nmcli con add type bond-slave ifname ens34 master bond0
```

Set IP:

```bash
nmcli con mod bond0 ipv4.addresses 192.168.10.90/24 ipv4.method manual
nmcli con up bond0
```

---

# 📘 8. Reverse Proxies and Load Balancers (Beginner-Friendly)

Reverse proxy:
Client → Proxy → Backend servers
Used for security, caching, load balancing.

Common tools:

* **Nginx**
* **HAProxy**
* **Apache httpd mod_proxy**

---

## ⭐ 8.1 Simple Reverse Proxy with Nginx

Install:

```bash
sudo dnf install nginx -y
sudo systemctl enable --now nginx
```

Edit config:

```bash
sudo nano /etc/nginx/conf.d/reverse.conf
```

Add:

```nginx
server {
    listen 80;

    location / {
        proxy_pass http://127.0.0.1:8080;
    }
}
```

Restart:

```bash
sudo systemctl restart nginx
```

Now Nginx forwards all traffic → port 8080.

---

## ⭐ 8.2 Simple Load Balancing with Nginx

Add:

```nginx
upstream app_backend {
    server 192.168.10.101;
    server 192.168.10.102;
}

server {
    listen 80;

    location / {
        proxy_pass http://app_backend;
    }
}
```

Test:

```bash
curl http://server/
```

Requests will alternate between backend servers.

---

## ⭐ 8.3 Load Balancing with HAProxy (optional but useful)

Install:

```bash
sudo dnf install haproxy -y
```

Edit:

```bash
/etc/haproxy/haproxy.cfg
```

Example:

```
frontend web
    bind *:80
    default_backend apps

backend apps
    balance roundrobin
    server app1 192.168.10.101:80
    server app2 192.168.10.102:80
```

Enable:

```bash
sudo systemctl enable --now haproxy
```

---

# 📘 Summary Cheat Sheet

```
IPv4 static IP:      nmcli con mod ens33 ipv4.addresses "X/X"
IPv6 static IP:      nmcli con mod ens33 ipv6.addresses "X/X"
DNS:                 nmcli con mod ens33 ipv4.dns "8.8.8.8"
Hostname:            hostnamectl set-hostname name
Chrony:              chronyc sources
Firewall:            firewall-cmd --add-service=http --permanent
Static Route:        nmcli con mod ens33 +ipv4.routes "net gw"
Bridge:              nmcli con add type bridge ifname br0
Bond:                nmcli con add type bond ifname bond0 mode active-backup
SSH server:          systemctl enable --now sshd
Reverse proxy:       proxy_pass http://backend
Load balance:        upstream { server1; server2; }
```

---

# ✔ End of Notes

These notes cover everything needed for **LFCS Networking**, explained from a beginner perspective with complete examples.

```

---

