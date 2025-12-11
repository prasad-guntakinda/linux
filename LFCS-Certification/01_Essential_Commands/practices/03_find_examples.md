Below is a **beginner-friendly + LFCS-ready** guide with **practical examples** of using the `find` command to search for files.
This includes basic → advanced usage, plus filtering by name, size, permissions, type, owner, time, and actions.

All examples work on RHEL/Rocky/AlmaLinux systems and are formatted for GitHub.

---

# 📘 **1. Basic find command usage**

### ✔ Find all files under current directory

```bash
find .
```

### ✔ Find files under a specific directory

```bash
find /var/log
```

---

# 📘 **2. Find by File Name**

### ✔ Exact name match

```bash
find /etc -name hosts
```

### ✔ Case-insensitive match

```bash
find /etc -iname hosts
```

### ✔ Files ending with `.log`

```bash
find /var/log -name "*.log"
```

### ✔ Files starting with `config`

```bash
find /etc -name "config*"
```

---

# 📘 **3. Find by Type**

| Type      | Meaning   |
| --------- | --------- |
| `-type f` | file      |
| `-type d` | directory |
| `-type l` | symlink   |

### ✔ All directories under `/etc`

```bash
find /etc -type d
```

### ✔ All symbolic links

```bash
find / -type l 2>/dev/null
```

### ✔ Only regular files

```bash
find . -type f
```

---

# 📘 **4. Find by Size**

| Option  | Meaning      |
| ------- | ------------ |
| `+`     | greater than |
| `-`     | less than    |
| no sign | exact        |

### ✔ Files larger than 10MB

```bash
find /var -type f -size +10M
```

### ✔ Files smaller than 100KB

```bash
find /home -type f -size -100k
```

### ✔ Files exactly 1GB

```bash
find . -type f -size 1G
```

---

# 📘 **5. Find by Owner / Group**

### ✔ Files owned by user `apache`

```bash
find /var/www -user apache
```

### ✔ Files owned by group `devteam`

```bash
find /project -group devteam
```

---

# 📘 **6. Find by Permission**

### ✔ All world-writable files (`others` have write permission)

```bash
find / -type f -perm -o=w 2>/dev/null
```

### ✔ Files with 777 permissions

```bash
find /tmp -type f -perm 777
```

### ✔ Files with any write permission for group

```bash
find /data -perm -g=w
```

### ✔ Directories where group has execute permission

```bash
find /project -type d -perm -g=x
```

---

# 📘 **7. Find by Modification Time**

| Option      | Meaning            |
| ----------- | ------------------ |
| `-mtime +7` | older than 7 days  |
| `-mtime -7` | within last 7 days |
| `-mtime 0`  | modified today     |

### ✔ Files modified in last 24 hours

```bash
find /var/log -mtime -1
```

### ✔ Files modified more than 30 days ago

```bash
find /backup -mtime +30
```

### ✔ Files accessed within last 10 days

```bash
find /data -atime -10
```

---

# 📘 **8. Find and Execute Actions**

## ⭐ Using `-delete`

### ✔ Delete all `.tmp` files

```bash
find /tmp -name "*.tmp" -type f -delete
```

⚠ Be careful! Always test with `-print` first.

---

## ⭐ Using `-exec`

### ✔ Change permissions of all `.sh` files

```bash
find /scripts -name "*.sh" -type f -exec chmod 755 {} \;
```

### ✔ Remove all empty files

```bash
find /var/log -type f -empty -exec rm -v {} \;
```

### ✔ Replace owner for all files in `/project`

```bash
find /project -type f -exec chown devuser:devteam {} \;
```

---

## ⭐ Using `-exec` with multiple actions

Example:

```bash
find /logs -name "*.log" -exec gzip {} \; -exec mv {}.gz /archive \;
```

---

# 📘 **9. Find only empty files or directories**

### ✔ Empty files

```bash
find /var -type f -empty
```

### ✔ Empty directories

```bash
find /var -type d -empty
```

---

# 📘 **10. Find files and print additional info**

### ✔ Show file size with results

```bash
find /var/log -type f -exec ls -lh {} \;
```

### ✔ Show inode numbers

```bash
find . -type f -printf "%i %p\n"
```

---

# 📘 **11. Find Files Excluding Certain Directories**

### ✔ Ignore `/proc` and `/sys`

```bash
find / -path /proc -prune -o -path /sys -prune -o -type f -print
```

---

# 📘 **12. Combine Multiple Conditions**

### ✔ Find `.log` files larger than 10MB

```bash
find /var/log -name "*.log" -size +10M
```

### ✔ Find world-writable files owned by user `nobody`

```bash
find / -type f -user nobody -perm -o=w 2>/dev/null
```

### ✔ Find files not accessed in 90 days AND larger than 100MB

```bash
find /data -atime +90 -size +100M
```

---

# 📘 **13. LFCS Exam-Style Examples**

### ❓ **Q1: Find all files under `/var/log` that are larger than 5MB.**

```bash
find /var/log -type f -size +5M
```

---

### ❓ **Q2: Find all directories under `/home` owned by user `john`.**

```bash
find /home -type d -user john
```

---

### ❓ **Q3: Find world-writable files in `/tmp`.**

```bash
find /tmp -type f -perm -o=w
```

---

### ❓ **Q4: Delete all `.bak` files older than 7 days.**

```bash
find / -name "*.bak" -mtime +7 -delete
```

---

### ❓ **Q5: Compress all `.log` files older than 3 days.**

```bash
find /var/log -name "*.log" -mtime +3 -exec gzip {} \;
```

---

If you want, I can also give:

✔ **find command cheat sheet**
✔ **Advanced find + xargs examples**
✔ **LFCS command-line practice test (50 questions)**
