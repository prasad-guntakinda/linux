
Perfect 👍
Below are **30 LFCS-style mock questions on Users & Groups**, carefully designed to **match real exam difficulty** and **cover all requested topics**.

> 📌 **Important**

* These are **hands-on tasks**, not theory
* Assume you have **root access**
* Do **only what is asked** (LFCS is strict)
* Distribution matches exam weight

---

# 🔐 LFCS MOCK QUESTIONS – USERS & GROUPS (30)

---

## 🧑‍💻 A. Create and Manage Local User and Group Accounts (12 Questions)

### Q1

Create a user named `dev1` with:

* Home directory `/home/dev1`
* Default shell `/bin/bash`

---

### Q2

Create a group called `engineering` with GID `4500`.

---

### Q3

Create a user `alice` with:

* Primary group `engineering`
* Secondary groups `wheel` and `docker`

---

### Q4

Create a system user `backupsvc`:

* No login shell
* No home directory

---

### Q5

Modify user `alice` so that:

* Her login shell is `/bin/zsh`

---

### Q6

Lock the account of user `dev1` without deleting it.

---

### Q7

Set the password of user `alice` to expire after **30 days**.

---

### Q8

Delete user `backupsvc` but **preserve its home directory** (if any).

---

### Q9

Create a user `tempuser` whose account expires on **31-Dec-2025**.

---

### Q10

Change the UID of user `tempuser` to `5500`.

---

### Q11

Ensure user `alice` cannot login interactively but can still own files.

---

### Q12

List all users whose login shell is `/bin/bash`.

---

---

## 🌍 B. Personal & System-Wide Environment Profiles (6 Questions)

### Q13

Set an environment variable `APP_ENV=production` for **all users** on the system.

---

### Q14

Configure user `alice` so that:

* Variable `EDITOR=vim` is set only for her

---

### Q15

Ensure that `/opt/tools/bin` is added to the PATH:

* For all users
* Without modifying individual `.bashrc` files

---

### Q16

Configure a message of the day (MOTD) that displays:

```
Authorized access only
```

on login.

---

### Q17

Ensure that users logging in via SSH receive the same environment variables as local logins.

---

### Q18

Verify which file sets environment variables system-wide for bash users.

---

---

## ⏱️ C. Configure User Resource Limits (6 Questions)

### Q19

Limit user `alice` to:

* Maximum **10 processes**
* Soft limit only

---

### Q20

Configure user `dev1` so that:

* Maximum open files = `1024`
* Applies on every login

---

### Q21

Set a **hard limit** on memory usage (virtual memory) for group `engineering` to `500MB`.

---

### Q22

Verify the current resource limits applied to user `alice`.

---

### Q23

Ensure limits configured for users are persistent after reboot.

---

### Q24

Prevent user `tempuser` from consuming more than **5 minutes of CPU time**.

---

---

## 📁 D. Configure and Manage ACLs (4 Questions)

### Q25

Create directory `/shared` such that:

* Group `engineering` has read/write/execute access
* Others have no access

---

### Q26

Grant user `alice` **read-only access** to `/shared/report.txt` using ACLs.

---

### Q27

Ensure all new files created inside `/shared` automatically grant:

* Read access to user `dev1`

---

### Q28

Remove all ACL entries from `/shared/report.txt`.

---

---

## 🔐 E. LDAP User and Group Accounts (Concept + Config) (2 Questions)

> ⚠️ LFCS expects **basic configuration**, not full LDAP server setup

### Q29

Configure the system to use LDAP for authentication:

* Ensure local users can still log in
* LDAP users should be visible via `getent passwd`

---

### Q30

Verify that LDAP users and groups are correctly resolved using NSS.

---

---

## ✅ How to Practice These (Recommended)

* Time yourself: **90 minutes**
* Verify after each task:

  ```bash
  id username
  getent passwd
  getfacl file
  ulimit -a
  ```
* If unsure → use:

  ```bash
  man useradd
  man login.defs
  man limits.conf
  ```

---

## 🔜 Next Steps (Your Choice)

I can now:
1️⃣ Provide **FULL ANSWERS + COMMANDS**
2️⃣ Create a **timed mini mock (10 questions / 30 min)**
3️⃣ Convert this into a **printable Markdown exam sheet**
4️⃣ Simulate **real LFCS grading style**

👉 Just tell me what you want next, Prasad 🚀



---
---

# ✅ LFCS – USERS & GROUPS

## FULL ANSWERS + COMMANDS (30/30)

---

## 🧑‍💻 A. Create and Manage Local User and Group Accounts

---

### ✅ Q1. Create user `dev1`

```bash
useradd -m -s /bin/bash dev1
```

Verify:

```bash
id dev1
```

---

### ✅ Q2. Create group `engineering` with GID 4500

```bash
groupadd -g 4500 engineering
```

---

### ✅ Q3. Create user `alice` with primary & secondary groups

```bash
useradd -g engineering -G wheel,docker alice
```

---

### ✅ Q4. Create system user `backupsvc`

```bash
useradd -r -s /sbin/nologin -M backupsvc
```

---

### ✅ Q5. Change shell for `alice`

```bash
usermod -s /bin/zsh alice
```

---

### ✅ Q6. Lock user `dev1`

```bash
usermod -L dev1
```

Verify:

```bash
passwd -S dev1
```

---

### ✅ Q7. Password expiry for `alice` (30 days)

```bash
chage -M 30 alice
```

---

### ✅ Q8. Delete user but keep home

```bash
userdel backupsvc
```

⚠️ **Do NOT use `-r`**

---

### ✅ Q9. Create user `tempuser` with expiry

```bash
useradd -e 2025-12-31 tempuser
```

---

### ✅ Q10. Change UID of `tempuser`

```bash
usermod -u 5500 tempuser
```

---

### ✅ Q11. Disable interactive login for `alice`

```bash
usermod -s /sbin/nologin alice
```

✔ Files remain owned by user

---

### ✅ Q12. List users with `/bin/bash`

```bash
awk -F: '$7=="/bin/bash"{print $1}' /etc/passwd
```

---

## 🌍 B. Environment Profiles

---

### ✅ Q13. Set an environment variable APP_ENV=production for all users on the system.
System-wide variable `APP_ENV`

```bash
echo 'export APP_ENV=production' > /etc/profile.d/app_env.sh
chmod +x /etc/profile.d/app_env.sh
```

---

### ✅ Q14. User-only variable for `alice`
Configure user alice so that: Variable EDITOR=vim is set only for her

```bash
echo 'export EDITOR=vim' >> /home/alice/.bashrc
chown alice:alice /home/alice/.bashrc
```

---

### ✅ Q15. Add PATH system-wide
Ensure that /opt/tools/bin is added to the PATH:

For all users

Without modifying individual .bashrc files

```bash
echo 'export PATH=$PATH:/opt/tools/bin' > /etc/profile.d/tools.sh
chmod +x /etc/profile.d/tools.sh
```

---

### ✅ Q16. Configure MOTD
Configure a message of the day (MOTD) that displays:

```bash
echo "Authorized access only" > /etc/motd
```

---

### ✅ Q17. SSH users get same env

Ensure this line exists:

```bash
grep -q UsePAM /etc/ssh/sshd_config || echo "UsePAM yes" >> /etc/ssh/sshd_config
systemctl restart sshd
```

---

### ✅ Q18. System-wide env file
which file sets environment variables system-wide for bash users?
```text
/etc/profile
/etc/profile.d/*.sh
```

---

## ⏱️ C. User Resource Limits

---

### ✅ Q19. Soft process limit for `alice`

```bash
echo "alice soft nproc 10" >> /etc/security/limits.conf
```

---

### ✅ Q20. Open file limit for `dev1`

```bash
echo "dev1 soft nofile 1024" >> /etc/security/limits.conf
```

---

### ✅ Q21. Hard memory limit for group

```bash
echo "@engineering hard as 500000" >> /etc/security/limits.conf
```

(as = address space in KB)

---

### ✅ Q22. Verify limits

```bash
su - alice
ulimit -a
```

---

### ✅ Q23. Make limits persistent

✔ Already persistent via:

```text
/etc/security/limits.conf
/etc/security/limits.d/
```

Ensure PAM:

```bash
grep pam_limits /etc/pam.d/*
```

---

### ✅ Q24. CPU time limit for `tempuser`

```bash
echo "tempuser hard cpu 300" >> /etc/security/limits.conf
```

(300 seconds = 5 minutes)

---

## 📁 D. ACLs

---

### ✅ Q25. Directory with group permissions

```bash
mkdir /shared
chown root:engineering /shared
chmod 770 /shared
```

---

### ✅ Q26. ACL read-only access

```bash
setfacl -m u:alice:r /shared/report.txt
```

---

### ✅ Q27. Default ACL for new files

```bash
setfacl -d -m u:dev1:r /shared
```

Verify:

```bash
getfacl /shared
```

---

### ✅ Q28. Remove all ACLs

```bash
setfacl -b /shared/report.txt
```

---

## 🔐 E. LDAP User & Group Accounts

---

### ✅ Q29. Configure LDAP authentication

Install packages:

```bash
dnf install -y sssd sssd-ldap oddjob-mkhomedir authselect
```

Enable LDAP profile:

```bash
authselect select sssd with-mkhomedir --force
```

Edit:

```bash
vi /etc/sssd/sssd.conf
```

Basic config:

```ini
[sssd]
services = nss, pam
domains = LDAP

[domain/LDAP]
id_provider = ldap
ldap_uri = ldap://ldap.example.com
ldap_search_base = dc=example,dc=com
```

Permissions:

```bash
chmod 600 /etc/sssd/sssd.conf
systemctl enable --now sssd
```

---

### ✅ Q30. Verify LDAP users/groups

```bash
getent passwd
getent group
id ldapuser
```

✔ Confirms NSS + LDAP integration

---

# 🧠 EXAM GOLDEN RULES

* **Always verify**
* **Avoid unnecessary flags**
* **Prefer limits.conf over ulimit**
* **ACLs > chmod when asked**
* **LDAP = visibility, not server build**

---

## 🚀 Next?

I can now:
1️⃣ Run a **90-minute real LFCS mock**
2️⃣ Give **users & groups 1-page cheat sheet**
3️⃣ Do **speed drills (5 mins / task)**

Just say 👉 **“Start full mock exam”** 💪
