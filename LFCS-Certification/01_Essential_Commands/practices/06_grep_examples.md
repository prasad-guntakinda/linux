# Searching Files Using `grep` — LFCS Notes + Examples

`grep` (Global Regular Expression Print) is one of the most important Linux commands for searching **text inside files**.

It supports:
- Simple text matching  
- Case-insensitive search  
- Searching directories recursively  
- Regex patterns  
- Highlighting matches  
- Counting matches  
- Showing line numbers  
- Filtering logs  

---

# =========================================
# 🟦 1. Basic grep Usage
# =========================================

## ✔ Search for a word inside a file
```bash
grep "error" logfile.txt
````

Finds all lines containing “error”.

---

## ✔ Highlight matches (default in many distro)

```bash
grep --color=auto "error" logfile.txt
```

---

## ✔ Case-insensitive search

```bash
grep -i "error" logfile.txt
```

Matches: ERROR, Error, eRrOr, etc.

---

## ✔ Count occurrences

```bash
grep -c "error" logfile.txt
```

Output example:

```
15
```

---

## ✔ Show line numbers

```bash
grep -n "error" logfile.txt
```

Example output:

```
23: error initializing module
45: critical error detected
```

---

# =========================================

# 🟦 2. Search in Multiple Files

# =========================================

## ✔ Search a word inside all `.log` files

```bash
grep "failed" *.log
```

---

## ✔ Search recursively in a directory (IMPORTANT)

```bash
grep -r "timeout" /var/log
```

Recursive search = searches all subdirectories.

---

## ✔ Recursive + case-insensitive

```bash
grep -ri "error" /var/log
```

---

## ✔ Show filename along with match

```bash
grep -H "error" *.conf
```

---

# =========================================

# 🟦 3. Useful Options

# =========================================

## ✔ Show only filenames that contain the match

```bash
grep -l "linux" *.txt
```

Opposite: show files that *do NOT* contain match:

```bash
grep -L "linux" *.txt
```

---

## ✔ Show only the matching text (not entire line)

```bash
grep -o "ERROR" /var/log/messages
```

---

## ✔ Invert match (show lines NOT containing pattern)

```bash
grep -v "DEBUG" app.log
```

This removes debug lines from log output.

---

## ✔ Match whole words only

```bash
grep -w "cat" animals.txt
```

This matches “cat” but **not** “category”.

---

## ✔ Search for multiple patterns

```bash
grep -e "error" -e "warning" logfile.txt
```

---

# =========================================

# 🟦 4. Regular Expression Examples

# =========================================

## ✔ Find lines starting with ERROR

```bash
grep "^ERROR" app.log
```

## ✔ Find lines ending with success

```bash
grep "success$" app.log
```

## ✔ Match any number

```bash
grep "[0-9]" file.txt
```

## ✔ Match IP addresses (simple example)

```bash
grep -E "[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+" access.log
```

`-E` enables extended regex.

---

# =========================================

# 🟦 5. Using grep with Pipes

# =========================================

## ✔ Filter running processes

```bash
ps aux | grep nginx
```

---

## ✔ Filter network connections

```bash
ss -tulpn | grep ssh
```

---

## ✔ See only errors in logs

```bash
journalctl -xe | grep -i error
```

---

## ✔ Find listening ports except systemd entries

```bash
ss -tuln | grep -v "systemd"
```

---

# =========================================

# 🟦 6. Search with Word Boundaries and Patterns

# =========================================

## ✔ Search complete words only

```bash
grep -w "root" /etc/passwd
```

## ✔ Search lines with 3 digits

```bash
grep -E "[0-9]{3}" data.txt
```

---

# =========================================

# 🟦 7. Combine grep with head/tail

# =========================================

## ✔ Show first 10 error lines

```bash
grep -i "error" /var/log/messages | head
```

## ✔ Show last 10 warnings

```bash
grep -i "warn" /var/log/messages | tail
```

---

# =========================================

# 🟦 8. grep in Real LFCS Tasks

# =========================================

### ❓ **Task 1:** Find all lines containing “sshd” in `/var/log/secure`

```bash
grep "sshd" /var/log/secure
```

---

### ❓ **Task 2:** Find how many failed SSH logins occurred

```bash
grep -c "Failed password" /var/log/secure
```

---

### ❓ **Task 3:** Recursively find all shell scripts containing `/bin/sh`

```bash
grep -r "/bin/sh" /home
```

---

### ❓ **Task 4:** Show only filenames that contain the word "Listen"

```bash
grep -rl "Listen" /etc
```

---

### ❓ **Task 5:** Remove all comment lines (#) from a config file

```bash
grep -v "^#" httpd.conf
```

---

### ❓ **Task 6:** Show only lines starting with a capital letter

```bash
grep "^[A-Z]" textfile
```

---

# =========================================

# 🟦 9. grep Quick Reference Table

# =========================================

| Task                | Command                  |
| ------------------- | ------------------------ |
| Basic search        | `grep "text" file`       |
| Case-insensitive    | `grep -i "text" file`    |
| Line numbers        | `grep -n "text" file`    |
| Count matches       | `grep -c "text" file`    |
| Recursive search    | `grep -r "text" dir/`    |
| Invert match        | `grep -v "text" file`    |
| Whole word          | `grep -w "text" file`    |
| Regex search        | `grep -E "pattern" file` |
| Show filenames only | `grep -l "text" *`       |
| Print match only    | `grep -o "pattern" file` |

---

# 🎉 End of grep Notes

```

---

If you want, I can also prepare:

📌 **grep practice exercises + solutions**  
📌 **grep regex cheat sheet**  
📌 **find + grep combo examples (very useful in LFCS)**
```
