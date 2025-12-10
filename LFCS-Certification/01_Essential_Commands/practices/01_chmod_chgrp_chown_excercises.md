Below is a **clean, LFCS-ready Markdown file** containing:

✅ **Linux file permissions practice exercises**
✅ **Solutions included**
✅ Covers `chmod`, `chown`, `chgrp`, special permissions (SUID, SGID, Sticky Bit)
✅ Beginner → advanced → LFCS exam-style scenarios

You can save this directly into your GitHub repo.

---

````markdown
# Linux File Permissions – Practice Exercises + Solutions (LFCS)

This document contains hands-on permission exercises with full solutions.  
Topics included:

- chmod (numeric + symbolic modes)
- chown (owner + group changes)
- chgrp (group changes)
- Special bits (SUID, SGID, Sticky Bit)
- Real LFCS-style troubleshooting tasks

---

# ============================
# 1️⃣ BASIC CHMOD EXERCISES
# ============================

---

## **Exercise 1**
Make the file `notes.txt` readable and writable **only by the owner**.

### ✅ Solution:
```bash
chmod 600 notes.txt
````

---

## **Exercise 2**

Add execute permission for **user only** on script `run.sh`.

### ✅ Solution:

```bash
chmod u+x run.sh
```

---

## **Exercise 3**

Remove write permission for **others** on `data.log`.

### ✅ Solution:

```bash
chmod o-w data.log
```

---

## **Exercise 4**

Give **read and execute** permissions to group and others on `backup.sh`.

### ✅ Solution:

```bash
chmod 755 backup.sh
```

---

## **Exercise 5**

Give **read permissions** to everyone on `inventory.txt`.

### ✅ Solution:

```bash
chmod a+r inventory.txt
```

---

# ============================

# 2️⃣ INTERMEDIATE PERMISSIONS

# ============================

---

## **Exercise 6**

Change `project/` directory so that **owner and group have full permissions**, others have **no permissions**.

### ✅ Solution:

```bash
chmod 770 project/
```

---

## **Exercise 7**

Add write permission for group on multiple files:

Files: `a.txt`, `b.txt`, `c.txt`

### ✅ Solution:

```bash
chmod g+w a.txt b.txt c.txt
```

---

## **Exercise 8**

Make all `.sh` files in a directory executable by everyone.

### ✅ Solution:

```bash
chmod a+x *.sh
```

---

## **Exercise 9**

Remove execute permission for **group and others** on a directory `bin/`.

### ✅ Solution:

```bash
chmod go-x bin/
```

---

## **Exercise 10**

Apply read/write permissions to group **recursively** on `/srv/reports`.

### ✅ Solution:

```bash
chmod -R g+rw /srv/reports
```

---

# ============================

# 3️⃣ OWNERSHIP EXERCISES (chown, chgrp)

# ============================

---

## **Exercise 11**

Change the owner of `app.conf` to `alice`.

### ✅ Solution:

```bash
chown alice app.conf
```

---

## **Exercise 12**

Change the group of `shared.txt` to `developers`.

### ✅ Solution:

```bash
chgrp developers shared.txt
```

---

## **Exercise 13**

Change both user and group of `/opt/tool` to `root:root`.

### ✅ Solution:

```bash
chown root:root /opt/tool
```

---

## **Exercise 14**

Recursively change group of `/var/www` to `apache`.

### ✅ Solution:

```bash
chgrp -R apache /var/www
```

---

## **Exercise 15**

Make user `bob` the owner of all `.log` files in `/logs`.

### ✅ Solution:

```bash
chown bob /logs/*.log
```

---

# ============================

# 4️⃣ SPECIAL PERMISSIONS

# ============================

---

## **Exercise 16 – SUID**

Set the SUID bit on `/usr/local/myprog`.

### Meaning: program runs with the **owner’s privileges**.

### ✅ Solution:

```bash
chmod u+s /usr/local/myprog
```

OR numeric:

```bash
chmod 4755 /usr/local/myprog
```

---

## **Exercise 17 – SGID**

Set SGID on `/shared/groupdata`.

### Meaning: new files inside inherit the directory’s **group**.

### ✅ Solution:

```bash
chmod g+s /shared/groupdata
```

OR numeric:

```bash
chmod 2755 /shared/groupdata
```

---

## **Exercise 18 – Sticky Bit**

Set sticky bit on `/common` so that **users can delete only their own files**.

### ✅ Solution:

```bash
chmod +t /common
```

OR numeric:

```bash
chmod 1777 /common
```

---

# ============================

# 5️⃣ TROUBLESHOOTING SCENARIOS (LFCS-Style)

# ============================

---

## **Exercise 19**

Users can't write to `/project`. Fix it so that:

* Group = `devteam`
* Group members have write access
* Others have no access

### ✅ Solution:

```bash
chgrp devteam /project
chmod 770 /project
```

---

## **Exercise 20**

Apache cannot read files in `/web/data`.
Fix it by giving Apache read access.

### Apache user/group: `apache`

### ✅ Solution:

```bash
chgrp -R apache /web/data
chmod -R 750 /web/data
```

---

## **Exercise 21**

User uploads fail under `/var/www/uploads`. The directory must be writable by Apache.

### Fix it:

### ✔ Apache RW directories should use:

`httpd_sys_rw_content_t` (SELinux)
`apache` group (ownership)
`770` or `775` permissions

### Minimal LFCS answer:

```bash
chgrp -R apache /var/www/uploads
chmod -R 775 /var/www/uploads
```

---

## **Exercise 22**

A script fails because it is not executable.
File: `/usr/local/bin/cleanup.sh`

### Make it executable by user only.

### ✅ Solution:

```bash
chmod 700 /usr/local/bin/cleanup.sh
```

---

## **Exercise 23**

In `/tmp/shared`, users should be able to create files but **only delete their own**.

### Use sticky bit.

### ✅ Solution:

```bash
chmod 1777 /tmp/shared
```

---

## **Exercise 24**

The directory `/apps` must allow:

* owner: full access
* group: read & execute
* others: no access

### Numeric permission?

### ✅ Solution:

```bash
chmod 750 /apps
```

---

## **Exercise 25**

Give **read/write** permission to everyone on `public.db`, but **do not give execute**.

### Numeric permission?

### ✅ Solution:

```bash
chmod 666 public.db
```

---

# ============================

# 6️⃣ ADVANCED PERMISSION TASKS

# ============================

---

## **Exercise 26**

Copy the permissions from file A to file B.

Use reference operator.

### File A: `/etc/passwd`

### File B: `config.ini`

### ✅ Solution:

```bash
chmod --reference=/etc/passwd config.ini
```

---

## **Exercise 27**

Set default permissions for new files in a directory using umask.
Required: new files should have permission `rw-r-----` (640).

Calculate umask:

Base: `666`
Desired: `640`

666 − 640 = **027**

### Set umask:

```bash
umask 027
```

---

## **Exercise 28**

You need to ensure that files created in `/devteam` inherit group ownership `devteam`.

### Use SGID:

### ✅ Solution:

```bash
chgrp devteam /devteam
chmod g+s /devteam
```

---

## **Exercise 29**

Give only execute permission to others while keeping all other permissions unchanged.

File: `build.sh`

### Symbolic mode?

### ✅ Solution:

```bash
chmod o+x build.sh
```

---

## **Exercise 30**

Remove write permission from group and others on `/secure/keys`.

### Symbolic mode?

### ✅ Solution:

```bash
chmod go-w /secure/keys
```

---

# 🎉 END OF PRACTICE EXERCISES + SOLUTIONS

If you want, I can also generate:

* ✔ A **permissions-only mock LFCS exam (50 questions)**
* ✔ A **permissions troubleshooting lab** (real-world scenarios)
* ✔ A **chown/chgrp/chmod combined cheat sheet**

Just tell me!

```

Let me know if you'd like more advanced permission labs or an LFCS simulation test!
```
