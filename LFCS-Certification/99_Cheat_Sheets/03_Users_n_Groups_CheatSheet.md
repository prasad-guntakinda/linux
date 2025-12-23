
## 🧑‍💻 USER & GROUP MANAGEMENT

```bash
useradd user1                     # create user (no home by default)
useradd -m user1                  # create user with home directory
useradd -m -s /bin/bash user1     # create user with bash shell
useradd -e 2025-12-31 user1       # create user with expiry date
useradd -r -s /usr/sbin/nologin svcuser  # system service user

usermod -aG dev user1             # add user to supplementary group
usermod -s /bin/zsh user1         # change login shell
usermod -L user1                  # lock user account
usermod -U user1                  # unlock user account

userdel user1                     # delete user (keep home)
userdel -r user1                  # delete user and home directory
```

```bash
groupadd dev                      # create group
groupadd -g 4500 dev              # create group with specific GID
groupdel dev                      # delete group
gpasswd -a user1 dev              # add user to group
gpasswd -d user1 dev              # remove user from group
```

```bash
passwd user1                      # set/change password
passwd -l user1                   # lock password
passwd -u user1                   # unlock password
chage -M 30 user1                 # password valid for 30 days
chage -E 2025-12-31 user1         # account expiry date
chage -l user1                    # view password aging info
```

```bash
id user1                          # show UID, GID, groups
getent passwd user1               # verify user exists
getent group dev                  # verify group exists
```

### Files:

```bash
/etc/passwd    → user info
/etc/shadow    → passwords & expiry
/etc/group     → group info
/etc/gshadow   → group passwords & expiry
/etc/login.defs → defaults
/etc/skel       → new user init files template dir
```

---

## 🌍 ENVIRONMENT VARIABLES & PROFILES

### System-wide (ALL users)

```bash
/etc/profile                      # global shell settings
/etc/profile.d/*.sh               # preferred place for custom vars
/etc/environment                  # simple KEY=VALUE (no export)
```

```bash
echo 'APP_ENV=prod' >> /etc/environment    # set variable for all users
```

```bash
echo 'export PATH=$PATH:/opt/tools/bin' > /etc/profile.d/tools.sh
chmod +x /etc/profile.d/tools.sh            # add PATH system-wide
```

### Per-user

```bash
~/.profile                        # login shell variables
~/.bashrc                         # interactive shell variables
```

```bash
echo 'export EDITOR=vim' >> ~/.bashrc   # user-specific variable
```

```bash
echo "Authorized access only" > /etc/motd  # message of the day
```

---

## ⏱️ USER RESOURCE LIMITS

### Config Files

```bash
/etc/security/limits.conf         # main limits file
/etc/security/limits.d/*.conf     # modular limits
```

### Syntax

```text
user|@group soft|hard resource value
```

### Common Resources

* `nproc` → number of processes
* `nofile` → open files
* `cpu` → CPU time (seconds)
* `as` → virtual memory (KB)

```bash
alice soft nproc 10               # max 10 processes (soft)
dev1 soft nofile 1024             # open files limit
@dev hard as 500000               # memory limit for group
tempuser hard cpu 300             # 5 minutes CPU time
```

```bash
ulimit -a                         # verify applied limits
```

> ⚠️ Logout/login required for limits to apply

---

## 📁 ACL (ACCESS CONTROL LISTS)

```bash
getfacl file.txt                  # view ACL entries
getfacl -R dir/
setfacl -m u:username:rwx file
setfacl -R -m u:username:rwx dir
setfacl -m u:alice:rw file.txt    # grant read/write to user
setfacl -m g:dev:rwx dir/         # grant group permissions
setfacl -d -m u:dev:r dir/        # default ACL for new files
setfacl -b file.txt               # remove all ACLs
```

```bash
ls -l                             # '+' indicates ACL present
```

```bash
getfacl -R /shared > acl.bak      # backup ACLs
setfacl --restore=acl.bak         # restore ACLs
```

---

## 🔐 LDAP USER & GROUP ACCOUNTS (CLIENT SIDE)

```bash
apt update
apt install -y sssd sssd-ldap libnss-sss libpam-sss ldap-utils
```

```bash
/etc/sssd/sssd.conf               # main LDAP client config
chmod 600 /etc/sssd/sssd.conf     # required permissions
```

```ini
[sssd]
services = nss, pam
domains = LDAP

[domain/LDAP]
id_provider = ldap
ldap_uri = ldap://ldap.example.com
ldap_search_base = dc=example,dc=com
```

```bash
systemctl enable --now sssd       # start LDAP client service
```

```bash
/etc/nsswitch.conf                # name resolution order
passwd: files sss
group:  files sss
shadow: files sss
```

```bash
getent passwd                     # list local + LDAP users
getent group                      # list local + LDAP groups
id ldapuser                       # verify LDAP user resolution
```

---

## 🎯 LFCS EXAM QUICK REMINDERS

✔ Use **ACL** when ownership change is not allowed
✔ Use **/etc/profile.d/** for system-wide env vars
✔ Limits → always via **limits.conf**
✔ LDAP → verify with **getent**, not login
✔ Always **verify after every task**
