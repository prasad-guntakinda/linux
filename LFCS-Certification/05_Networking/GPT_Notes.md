# Network


Great! Below are **all 3 items you requested**, each provided as **clean Markdown** so you can save them into your GitHub repo:

---


# ⭐ Everything is formatted in a single, well-organized **GitHub-ready Markdown file**.

---

# LFCS Networking Practice Questions + Solutions + One-Page Cheat Sheet (RHEL Systems)

This document contains:

1. **Networking Practice Questions (No solutions first)**
2. **Networking Practice Questions WITH detailed solutions**
3. **One-page Networking Cheat Sheet**

All examples are RHEL 8/9 compatible (Rocky/Alma/RHEL).

---

# ============================================
# 1️⃣ LFCS NETWORKING PRACTICE QUESTIONS
# ============================================

## 🟦 Section A — IPv4 / IPv6 Networking & Hostname

1. Configure an IPv4 static address on interface `ens33`:
   - IP: `192.168.50.10/24`
   - Gateway: `192.168.50.1`
   - DNS: `8.8.8.8`

2. Configure the same interface to use DHCP again.

3. Assign an IPv6 static address:
   - `2001:db8:abcd:1::50/64`
   - Gateway: `2001:db8:abcd:1::1`

4. Change system hostname to `web01.example.com`.

5. Add an entry to `/etc/hosts` so that `web01` resolves locally.

---

## 🟩 Section B — Time Synchronization (chrony)

6. Configure the system to use `pool.ntp.org` as the NTP server.

7. Check if time synchronization is active.

8. Force the system to immediately correct large time differences.

---

## 🟥 Section C — Monitoring & Troubleshooting

9. Find your default gateway.

10. Check DNS resolution for `redhat.com`.

11. List all listening TCP ports with process names.

12. Display IPv6 routing table.

13. Bring the `ens33` interface down and then back up.

---

## 🟧 Section D — OpenSSH Server & Client

14. Install and enable the SSH server.

15. Disable password authentication in SSH.

16. Allow only the user `admin` to log in through SSH.

17. Copy a file `report.txt` to a remote server at `/tmp/report.txt`.

---

## 🟨 Section E — Firewall / NAT / Port Redirection

18. Allow HTTP and HTTPS services through the firewall.

19. Open TCP port 8080 permanently.

20. Forward external port 8081 → internal port 80.

21. Enable NAT masquerading for outbound traffic.

---

## 🟪 Section F — Static Routing

22. Add a temporary IPv4 route:
    - Network: `10.20.0.0/24`
    - Gateway: `192.168.50.1`

23. Make the above route persistent.

---

## 🟫 Section G — Bridge & Bonding

24. Create a network bridge called `br0`.

25. Add interface `ens33` as a bridge slave.

26. Create a bonding interface `bond0` using mode **active-backup**.

27. Add interfaces `ens33` and `ens34` as bond slaves.

---

## 🟩 Section H — Reverse Proxies & Load Balancers

28. Configure Nginx to act as a reverse proxy to `http://127.0.0.1:8080`.

29. Configure Nginx load balancing between:
    - `192.168.50.11`
    - `192.168.50.12`

---

# ============================================
# 2️⃣ NETWORKING PRACTICE QUESTIONS — SOLUTIONS
# ============================================

## 🟦 Section A — IPv4 / IPv6 Networking

### 1. Configure IPv4 static address
```bash
nmcli con mod ens33 ipv4.addresses 192.168.50.10/24
nmcli con mod ens33 ipv4.gateway 192.168.50.1
nmcli con mod ens33 ipv4.dns "8.8.8.8"
nmcli con mod ens33 ipv4.method manual
nmcli con up ens33
````

---

### 2. Configure DHCP for IPv4

```bash
nmcli con mod ens33 ipv4.method auto
nmcli con up ens33
```

---

### 3. Configure IPv6 static address

```bash
nmcli con mod ens33 ipv6.addresses "2001:db8:abcd:1::50/64"
nmcli con mod ens33 ipv6.gateway "2001:db8:abcd:1::1"
nmcli con mod ens33 ipv6.method manual
nmcli con up ens33
```

---

### 4. Set hostname

```bash
hostnamectl set-hostname web01.example.com
```

---

### 5. Add /etc/hosts entry

Add:

```
192.168.50.10   web01
```

---

## 🟩 Section B — Time Sync (chrony)

### 6. Configure NTP server

Edit `/etc/chrony.conf`:

```
server pool.ntp.org iburst
```

Restart:

```bash
systemctl restart chronyd
```

---

### 7. Check time sync status

```bash
chronyc tracking
chronyc sources
```

---

### 8. Force time sync

```bash
chronyc makestep
```

---

## 🟥 Section C — Troubleshooting

### 9. Default gateway

```bash
ip route
```

---

### 10. DNS resolution

```bash
dig redhat.com
# or
host redhat.com
```

---

### 11. Listening TCP ports

```bash
ss -tulnp
```

---

### 12. IPv6 routing table

```bash
ip -6 route
```

---

### 13. Down/up interface

```bash
nmcli device disconnect ens33
nmcli device connect ens33
```

---

## 🟧 Section D — SSH

### 14. Install and enable SSH

```bash
dnf install -y openssh-server
systemctl enable --now sshd
```

---

### 15. Disable password authentication

Edit `/etc/ssh/sshd_config`:

```
PasswordAuthentication no
```

Restart:

```bash
systemctl restart sshd
```

---

### 16. Allow only admin user

In `/etc/ssh/sshd_config`:

```
AllowUsers admin
```

Restart:

```bash
systemctl restart sshd
```

---

### 17. Copy file via SCP

```bash
scp report.txt user@server:/tmp/report.txt
```

---

## 🟨 Section E — Firewall, Port Forwarding, NAT

### 18. Allow HTTP & HTTPS

```bash
firewall-cmd --permanent --add-service=http
firewall-cmd --permanent --add-service=https
firewall-cmd --reload
```

---

### 19. Open port 8080

```bash
firewall-cmd --permanent --add-port=8080/tcp
firewall-cmd --reload
```

---

### 20. Port forwarding (8081 → 80)

```bash
firewall-cmd --permanent --add-forward-port=port=8081:proto=tcp:toport=80
firewall-cmd --reload
```

---

### 21. Enable NAT (masquerading)

```bash
firewall-cmd --permanent --add-masquerade
firewall-cmd --reload
```

---

## 🟪 Section F — Static Routing

### 22. Temporary route

```bash
ip route add 10.20.0.0/24 via 192.168.50.1
```

---

### 23. Make route persistent

```bash
nmcli con mod ens33 +ipv4.routes "10.20.0.0/24 192.168.50.1"
nmcli con up ens33
```

---

## 🟫 Section G — Bridge & Bonding

### 24. Create bridge

```bash
nmcli con add type bridge ifname br0 con-name br0
```

### 25. Add ens33 to bridge

```bash
nmcli con add type bridge-slave ifname ens33 master br0
```

---

### 26. Create bonding interface

```bash
nmcli con add type bond ifname bond0 mode active-backup
```

### 27. Add slaves

```bash
nmcli con add type bond-slave ifname ens33 master bond0
nmcli con add type bond-slave ifname ens34 master bond0
```

---

## 🟩 Section H — Reverse Proxy / Load Balancing

### 28. Reverse proxy with Nginx

Create:

```
/etc/nginx/conf.d/reverse.conf
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
systemctl restart nginx
```

---

### 29. Load balancing

```
/etc/nginx/conf.d/lb.conf
```

```nginx
upstream backend {
    server 192.168.50.11;
    server 192.168.50.12;
}

server {
    listen 80;

    location / {
        proxy_pass http://backend;
    }
}
```

---

# ============================================

# 3️⃣ ONE-PAGE NETWORKING CHEAT SHEET (LFCS)

# ============================================

```
# IP Commands
ip a                    # show interfaces
ip route                # show routing table
ip -6 route             # IPv6 routes

# NMCLI
nmcli device status
nmcli con mod ens33 ipv4.addresses X/X
nmcli con up ens33

# Hostname
hostnamectl set-hostname name

# DNS
cat /etc/resolv.conf
dig host.com
host host.com

# Time Sync
systemctl enable --now chronyd
chronyc tracking
chronyc makestep

# Firewall
firewall-cmd --add-service=http --permanent
firewall-cmd --add-port=8080/tcp --permanent
firewall-cmd --add-forward-port=port=8081:proto=tcp:toport=80 --permanent
firewall-cmd --add-masquerade --permanent
firewall-cmd --reload

# SSH
dnf install openssh-server
systemctl enable --now sshd
ssh user@host
scp file user@host:/path/

# Routing
ip route add NET via GW
nmcli con mod ens33 +ipv4.routes "NET GW"

# Bridge
nmcli con add type bridge ifname br0
nmcli con add type bridge-slave ifname ens33 master br0

# Bonding
nmcli con add type bond ifname bond0 mode active-backup
nmcli con add type bond-slave ifname ens33 master bond0

# Reverse Proxy (nginx)
proxy_pass http://backend
```

---


