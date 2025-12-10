
Here are **clear, beginner-friendly + LFCS-ready examples** for the `chown` command, covering **basic → advanced** usage.

All examples are presented in **copy-paste-friendly** format.

---

# 🧩 **What `chown` Does**

`chown` = **CH**ange **OWN**er
It allows you to change:

* file owner
* file group
* both owner and group

---

# ✨ **1. Change File Owner**

```bash
sudo chown alice file.txt
```

Meaning:

* `alice` becomes the owner of `file.txt`
* Group ownership stays the same

---

# ✨ **2. Change File Group**

```bash
sudo chown :developers file.txt
```

Meaning:

* Only the **group** changes to `developers`
* Owner remains unchanged

---

# ✨ **3. Change Both Owner AND Group**

```bash
sudo chown alice:developers file.txt
```

Meaning:

* Owner becomes `alice`
* Group becomes `developers`

---

# ✨ **4. Change Ownership of a Directory Recursively**

Apply to folder + all files inside:

```bash
sudo chown -R alice:developers /project
```

`-R` = recursive

---

# ✨ **5. Give Ownership of a Folder to root**

```bash
sudo chown root:root /secure/logs
```

---

# ✨ **6. Change Ownership to a Numeric UID and GID**

```bash
sudo chown 1001:1002 file.txt
```

Useful in scripts or when usernames are unknown.

---

# ✨ **7. Change Group Only Using Empty Owner**

```bash
sudo chown :admins config.cfg
```

Owner remains unchanged.

---

# ✨ **8. Apply Ownership to Multiple Files**

```bash
sudo chown alice:staff file1 file2 file3
```

---

# ✨ **9. Change Ownership of All `.log` Files**

```bash
sudo chown bob:syslog *.log
```

---

# ✨ **10. Copy Ownership From Another File**

Use `--reference`:

```bash
sudo chown --reference=/etc/passwd config.txt
```

Meaning:

* Owner and group of `config.txt` will match the owner/group of `/etc/passwd`.

---

# ✨ **11. Change Ownership Only If File is a Symbolic Link Target**

Symbolic link handling:

### If you want to change the target file:

```bash
sudo chown -h alice linkname
```

### Normal behavior (changes target, not link itself):

```bash
sudo chown alice linkname
```

---

# ✨ **12. Set Folder Owner for Web Server**

Common in LFCS & real jobs:

```bash
sudo chown -R apache:apache /var/www/html
```

---

# ✨ **13. Remove Group Ownership (reset to default group of owner)**

```bash
sudo chown alice: file.txt
```

Group becomes the primary group of user `alice`.

---

# ✨ **14. Change Ownership Safely With Verbose Output**

```bash
sudo chown -v bob:users document.pdf
```

---

# 📌 **Common Mistakes to Avoid**

| Mistake                                 | Correct Action           |
| --------------------------------------- | ------------------------ |
| Forgetting `-R` for directories         | Use `chown -R`           |
| Trying to change ownership without sudo | Use `sudo`               |
| Confusing owner with permissions        | Owner ≠ permission flags |

---

# 🎯 LFCS Exam Notes

You MUST know how to:

* Change owner/group recursively
* Apply correct ownership for services (e.g., Apache, FTP)
* Fix permission issues caused by wrong ownership
* Use numeric UID/GID when required

Example exam-like task:

> Apache cannot write to `/web/data`. Fix ownership.

Solution:

```bash
sudo chown -R apache:apache /web/data
```

---

If you want, I can also give:

✔ **chown practice questions + solutions**
✔ **chown vs chmod comparison chart**
✔ **LFCS permission troubleshooting lab**

Would you like these?
