

# 📁 Configure and Manage ACLs (LFCS Learning Notes)

---

## 1️⃣ What is ACL?

**Access Control Lists (ACLs)** extend the traditional Linux permission model (`owner / group / others`) by allowing:

* Permissions for **specific users**
* Permissions for **specific groups**
* **Default permissions** for new files/directories

> 🔑 ACLs are used **when chmod/chown is not sufficient**

---

## 2️⃣ When to Use ACLs (Exam Scenarios)

Use ACLs when:

* Multiple users need different permissions on the **same file**
* You **cannot change ownership**
* Shared directories require **automatic permissions**
* Fine-grained access control is required

---

## 3️⃣ ACL Commands (Must Know)

### 🔹 View ACL

```bash
getfacl file_or_directory
```

---

### 🔹 Set ACL

```bash
setfacl [options] file_or_directory
```

---

### 🔹 Remove ACL

```bash
setfacl -b file
```

---

## 4️⃣ ACL Permission Types

| Type | Meaning                        |
| ---- | ------------------------------ |
| `u`  | User                           |
| `g`  | Group                          |
| `o`  | Others                         |
| `m`  | Mask (max allowed permissions) |
| `d`  | Default ACL                    |

---

## 5️⃣ Basic ACL Syntax

### 🔹 User ACL

```bash
setfacl -m u:alice:rwx file
```

### 🔹 Group ACL

```bash
setfacl -m g:engineering:rw file
```

### 🔹 Remove Specific ACL

```bash
setfacl -x u:alice file
```

---

## 6️⃣ ACL Mask (⚠️ VERY IMPORTANT)

### 🔹 What is Mask?

* Mask defines the **maximum effective permissions**
* Applies to **all named users and groups**

### 🔹 Example

```bash
setfacl -m u:alice:rwx file
setfacl -m m:rx file
```

➡ Effective permission = `r-x` (not `rwx`)

Check:

```bash
getfacl file
```

---

## 7️⃣ Default ACLs (Critical for Directories)

Default ACLs:

* Apply only to **directories**
* Automatically inherited by new files/directories

### 🔹 Set Default ACL

```bash
setfacl -d -m u:dev1:r directory
```

### 🔹 Verify

```bash
getfacl directory
```

---

## 8️⃣ Common LFCS Scenarios (With Examples)

---

### 🧪 Scenario 1: Grant user access without changing owner

```bash
setfacl -m u:alice:rw report.txt
```

---

### 🧪 Scenario 2: Shared directory with auto permissions

```bash
mkdir /shared
chown root:engineering /shared
chmod 770 /shared
setfacl -d -m g:engineering:rwx /shared
```

---

### 🧪 Scenario 3: Read-only access for one user

```bash
setfacl -m u:dev1:r file.txt
```

---

### 🧪 Scenario 4: Remove all ACLs

```bash
setfacl -b file.txt
```

---

## 9️⃣ ACL vs Traditional Permissions

| Feature             | chmod | ACL |
| ------------------- | ----- | --- |
| Per-user control    | ❌     | ✅   |
| Multiple groups     | ❌     | ✅   |
| Default inheritance | ❌     | ✅   |
| Fine-grained        | ❌     | ✅   |

---

## 🔟 ACL Indicator (`+` sign)

When ACL is applied:

```bash
ls -l
-rw-rw-r--+ 1 root engineering file.txt
```

➕ means **ACL exists**

---

## 1️⃣1️⃣ Backup & Restore ACLs

### 🔹 Backup

```bash
getfacl -R /shared > acl.backup
```

### 🔹 Restore

```bash
setfacl --restore=acl.backup
```

---

## 1️⃣2️⃣ ACL Filesystem Support

Verify ACL support:

```bash
mount | grep acl
```

Most modern filesystems (ext4, xfs) support ACL by default.

---

## 1️⃣3️⃣ Exam Pitfalls (VERY IMPORTANT)

❌ Forgetting **default ACL**
❌ Mask limiting permissions unexpectedly
❌ Using chmod after ACL (may modify mask)
❌ Setting ACL on files instead of directory defaults
❌ Forgetting verification (`getfacl`)

---

## 1️⃣4️⃣ LFCS Quick Practice Tasks

1️⃣ Give user `alice` RW access to `data.txt`
2️⃣ Ensure user `dev1` always has R access to `/shared`
3️⃣ Remove ACLs from a file
4️⃣ Identify why permissions are not effective (mask issue)

---

## 1️⃣5️⃣ Quick Verification Commands

```bash
getfacl file
ls -l
id username
```

---

## 🎯 LFCS Exam Tip (High Value)

> If the task says **“without changing ownership or permissions”** →
> 🔥 **ACL is mandatory**

---

If you want next, I can:

* ✅ Create **ACL-only mock exam (15 mins)**
* ✅ Share **1-page ACL cheat sheet**
* ✅ Do **live ACL troubleshooting scenarios**

Just tell me 👉 **“ACL mock”** 🚀
