Here are **beginner-friendly + LFCS-ready examples** for the `chgrp` command.
All examples are **copy–paste friendly** and cover basic → advanced usage.

---

# 🧩 **What `chgrp` Does**

`chgrp` = **CH**ange **GR**ou**P**
It allows you to change **only the group ownership** of a file or directory.

If you need to change BOTH owner and group → use `chown user:group file`.

---

# ⭐ **1. Change Group of a File**

```bash
sudo chgrp developers file.txt
```

Meaning:

* Group becomes **developers**
* Owner stays the same

---

# ⭐ **2. Change Group for Multiple Files**

```bash
sudo chgrp staff file1 file2 file3
```

---

# ⭐ **3. Change Group Recursively**

Useful when updating a whole directory tree.

```bash
sudo chgrp -R webadmins /var/www
```

This modifies the group of:

* `/var/www`
* all files
* all subdirectories

---

# ⭐ **4. Change Group Using GID Instead of Group Name**

```bash
sudo chgrp 1002 report.log
```

Good for scripts or when group names are unknown.

---

# ⭐ **5. Change Group of All `.log` Files**

```bash
sudo chgrp syslog *.log
```

---

# ⭐ **6. Change Group of a Directory Only (Not Recursive)**

```bash
sudo chgrp admins /opt/tools
```

---

# ⭐ **7. Change Only Group for a Symbolic Link Target**

By default, `chgrp` changes the **target file**, not the link.

```bash
sudo chgrp users symlink_name
```

To change the link itself (rare):

```bash
sudo chgrp -h users symlink_name
```

---

# ⭐ **8. Change Group and Show Verbose Output**

```bash
sudo chgrp -v devteam script.sh
```

Output example:

```
changed group of 'script.sh' from root to devteam
```

---

# ⭐ **9. Use `--reference` to Copy Group Ownership**

```bash
sudo chgrp --reference=/etc/passwd config.ini
```

Meaning:

* `config.ini` will take the **same group** as `/etc/passwd`.

---

# ⭐ **10. Fix Group Ownership for Web Server Directory**

Common LFCS scenario:

```bash
sudo chgrp -R apache /var/www/html
```

---

# ⭐ **11. Change Group of a Mounted Directory**

```bash
sudo chgrp -R users /mnt/shared
```

---

# ⭐ **12. Remove Group Ownership?**

Linux does **not** allow removing group ownership entirely (unlike ACLs).
But you can change it to the user’s primary group:

```bash
sudo chgrp $(id -gn alice) file.txt
```

---

# 📌 **chgrp vs chown: What’s the difference?**

| Command                 | Affects Owner? | Affects Group? |
| ----------------------- | -------------- | -------------- |
| `chgrp group file`      | ❌ No           | ✔ Yes          |
| `chown user file`       | ✔ Yes          | ❌ No           |
| `chown user:group file` | ✔ Yes          | ✔ Yes          |

---

# 🎯 **LFCS Exam Tips**

You must be able to:

* Fix incorrect group ownership
* Set correct web/ftp/database folder group permissions
* Apply recursive group changes
* Combine `chgrp` with ACLs (if needed)

Example exam-like question:

> *"Give group `developers` access to `/project` directory and ensure all subfolders use this group."*

Solution:

```bash
sudo chgrp -R developers /project
sudo chmod -R g+rwx /project
```

---

If you want, I can also provide:

👉 **chgrp practice questions + solutions**
👉 **chmod + chown + chgrp combined exercises**
👉 **File permissions cheat sheet for LFCS**

Would you like those?
