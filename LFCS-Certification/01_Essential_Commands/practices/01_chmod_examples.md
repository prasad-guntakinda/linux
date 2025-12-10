Here are **clear, beginner-friendly + LFCS-focused examples** for the `chmod` command.
These examples cover **numeric mode**, **symbolic mode**, **recursive mode**, **special permissions**, and **real exam-style use cases**.

All examples are **copy-paste friendly**.

---

# 🧩 **What `chmod` Does**

`chmod` = **CH**ange **MOD**e (permissions)

It modifies file/directory permissions for:

* **User (u)**
* **Group (g)**
* **Others (o)**
* **All (a)**

Using either:

* **Numeric mode** → `chmod 755 file`
* **Symbolic mode** → `chmod u+r file`

---

# ============================

# ✅ 1. NUMERIC MODE EXAMPLES

# ============================

| Number | Permission             |
| ------ | ---------------------- |
| 7      | read + write + execute |
| 6      | read + write           |
| 5      | read + execute         |
| 4      | read                   |
| 3      | write + execute        |
| 2      | write                  |
| 1      | execute                |
| 0      | no permission          |

---

## ⭐ Example 1: Give owner full access, group/others read+execute

```bash
chmod 755 script.sh
```

Meaning:

* user = 7 → rwx
* group = 5 → r-x
* others = 5 → r-x

---

## ⭐ Example 2: Make a file readable only by owner

```bash
chmod 600 secret.txt
```

---

## ⭐ Example 3: Make a directory accessible to everyone

```bash
chmod 777 /tmp/share
```

*(Avoid 777 in production, but useful for learning.)*

---

## ⭐ Example 4: Remove all permissions for others

```bash
chmod 770 project/
```

---

# ============================

# ✅ 2. SYMBOLIC MODE EXAMPLES

# ============================

## ⭐ Example 1: Add execute permission for the user

```bash
chmod u+x script.sh
```

---

## ⭐ Example 2: Add write permission for group

```bash
chmod g+w file.txt
```

---

## ⭐ Example 3: Remove read permission for others

```bash
chmod o-r file.txt
```

---

## ⭐ Example 4: Give read/write permissions to all

```bash
chmod a+rw shared.txt
```

---

## ⭐ Example 5: Remove execute for user and group

```bash
chmod ug-x myapp
```

---

# ============================

# ✅ 3. RECURSIVE EXAMPLES

# ============================

Apply permissions to directories + all files inside.

## ⭐ Example 1: Give group read/write to whole directory

```bash
chmod -R g+rw /data
```

---

## ⭐ Example 2: Remove other permissions recursively

```bash
chmod -R o-rwx /secure
```

---

# ============================

# ✅ 4. EXECUTABLE PERMISSIONS

# ============================

## ⭐ Example 1: Make a script executable

```bash
chmod +x script.sh
```

## ⭐ Example 2: Make all `.sh` files executable

```bash
chmod +x *.sh
```

---

# ============================

# ✅ 5. SPECIAL PERMISSIONS (SUID, SGID, Sticky Bit)

# ============================

## ⭐ Example 1: Set SUID (user ID bit)

```bash
chmod 4755 program
```

Meaning: program runs with owner's privileges.

---

## ⭐ Example 2: Set SGID (group ID bit)

```bash
chmod 2755 shared_dir
```

Meaning: all new files inherit the **directory group**.

---

## ⭐ Example 3: Set sticky bit (for shared dirs like /tmp)

```bash
chmod 1777 /shared
```

Meaning: users can only delete their own files.

---

## ⭐ Symbolic Mode Versions:

SUID:

```bash
chmod u+s program
```

SGID:

```bash
chmod g+s project_dir
```

Sticky bit:

```bash
chmod o+t /shared
```

---

# ============================

# ✅ 6. COMMON REAL-LIFE EXAMPLES

# ============================

## ⭐ Example 1: Web directory accessible by Apache

```bash
chmod 755 /var/www/html
```

---

## ⭐ Example 2: Private SSH key must be `600` (LFCS exam!)

```bash
chmod 600 ~/.ssh/id_rsa
```

---

## ⭐ Example 3: Logs writable only by owner

```bash
chmod 600 /var/log/app.log
```

---

## ⭐ Example 4: Allow a group to modify a directory

```bash
chmod 770 /project
```

---

# ============================

# 🎯 LFCS EXAM-STYLE QUESTIONS

# ============================

### ❓Q1: Make `/secure/data.txt` readable only by root.

**Answer:**

```bash
chmod 600 /secure/data.txt
```

---

### ❓Q2: Allow group members to write to `/share`.

**Answer:**

```bash
chmod g+w /share
```

---

### ❓Q3: Ensure `/common` allows deletion only by file owners.

**Answer:**

```bash
chmod 1777 /common
```

---

### ❓Q4: Make `/opt/tools/run.sh` executable by everyone.

**Answer:**

```bash
chmod a+x /opt/tools/run.sh
```

---

If you'd like, I can also provide:

✔ `chmod`, `chown`, `chgrp` **comparison table**
✔ **Practice exercises + solutions**
✔ **Permissions cheat sheet**
✔ **LFCS permission troubleshooting labs**
