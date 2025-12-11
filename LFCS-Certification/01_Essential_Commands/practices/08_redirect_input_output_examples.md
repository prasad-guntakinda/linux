Below is a **clean, beginner-friendly + LFCS-ready Markdown document** covering **Redirecting Input & Output in Linux**.

This includes:

* Output redirection
* Input redirection
* Append vs overwrite
* Redirecting errors
* Redirecting both output & errors
* Pipelines
* Here-documents
* Here-strings
* Practical LFCS examples

You can save this directly to your GitHub notes.

---

````markdown
# Redirecting Input & Output — LFCS Notes + Examples

Redirecting input/output is a core Linux skill.  
It allows you to send command results into files, combine commands, and control where errors go.

Linux has 3 standard streams:

| Stream | File Descriptor | Description |
|--------|-----------------|-------------|
| STDIN  | 0 | Input to a program |
| STDOUT | 1 | Normal output |
| STDERR | 2 | Error messages |

---

# ============================================
# 1️⃣ Redirecting Output (STDOUT)
# ============================================

### ✔ Overwrite a file (`>`)
Write output to a file, replacing existing content:
```bash
echo "Hello" > file.txt
````

If `file.txt` existed, it is overwritten.

---

### ✔ Append to a file (`>>`)

```bash
echo "Hello Again" >> file.txt
```

Adds content to the end of the file.

---

### ✔ Redirect command output to a file

```bash
ls -l > listing.txt
```

Append version:

```bash
ls -l >> listing.txt
```

---

# ============================================

# 2️⃣ Redirecting Input (STDIN)

# ============================================

Use `<` to feed a file into a command.

### ✔ Example: Count words in a file

```bash
wc -l < file.txt
```

### ✔ Sort using a file as input

```bash
sort < names.txt
```

---

# ============================================

# 3️⃣ Redirecting Error Messages (STDERR)

# ============================================

STDERR uses file descriptor `2`.

### ✔ Redirect errors to a file

```bash
ls /nonexistent 2> errors.txt
```

Append errors:

```bash
ls /nonexistent 2>> errors.txt
```

---

# ============================================

# 4️⃣ Redirecting BOTH Output & Errors

# ============================================

### ✔ Method 1: Redirect each separately

```bash
command > output.txt 2> error.txt
```

---

### ✔ Method 2: Merge STDERR into STDOUT

```bash
command > all.txt 2>&1
```

Meaning:

* `> all.txt` → redirect stdout
* `2>&1` → send stderr to same file

---

### ✔ Method 3: Redirect both STDOUT & STDERR (modern)

```bash
command &> file.txt
```

Append version:

```bash
command &>> file.txt
```

---

# ============================================

# 5️⃣ Pipes (|) — Send output to another command

# ============================================

Pipes take STDOUT of one command and send it as STDIN to another.

### ✔ Example: View logs with grep

```bash
journalctl -xe | grep ssh
```

---

### ✔ Sort process list by memory usage

```bash
ps aux | sort -k4 -nr
```

---

### ✔ Show top 10 IP addresses from a log

```bash
cut -d' ' -f1 access.log | sort | uniq -c | sort -nr | head
```

---

# ============================================

# 6️⃣ Here-Documents (<<)

# ============================================

A Here-Document sends **multiple lines of text** to a command.

### ✔ Example:

```bash
cat << EOF > message.txt
This is line 1
This is line 2
EOF
```

Creates:

```
This is line 1
This is line 2
```

---

### ✔ Use with a command:

```bash
grep "apple" <<EOF
apple pie
banana
cherry
EOF
```

---

# ============================================

# 7️⃣ Here-Strings (<<<)

# ============================================

Sends a **single string** as input to a command.

### ✔ Example:

```bash
grep oo <<< "food"
```

### ✔ Another:

```bash
wc -w <<< "hello world linux"
```

Output:

```
3
```

---

# ============================================

# 8️⃣ Redirecting to /dev/null (Discard Output)

# ============================================

Useful for ignoring output or errors.

### ✔ Discard normal output

```bash
command > /dev/null
```

### ✔ Discard only errors

```bash
command 2> /dev/null
```

### ✔ Discard both

```bash
command > /dev/null 2>&1
```

---

# ============================================

# 9️⃣ LFCS Practical Examples

# ============================================

### ✔ Example 1: Save only errors from log analysis

```bash
grep -r "error" /var/log 2> errors.txt
```

---

### ✔ Example 2: Create a file using redirection

```bash
cat > config.txt <<EOF
host=localhost
port=8080
EOF
```

---

### ✔ Example 3: Log output of a backup script

```bash
./backup.sh >> backup.log 2>&1
```

---

### ✔ Example 4: Redirect file content into a program

```bash
sort < list.txt > sorted.txt
```

---

### ✔ Example 5: Ignore unwanted messages

```bash
apt update 2> /dev/null
```

---

### ✔ Example 6: Pipe multiple commands

```bash
ls -l | grep "^d" | wc -l
```

Counts directories in current directory.

---

# ============================================

# 🔟 Summary Table (Exam-Ready)

# ============================================

| Task            | Command               |           |
| --------------- | --------------------- | --------- |
| Redirect output | `>`                   |           |
| Append output   | `>>`                  |           |
| Redirect input  | `<`                   |           |
| Redirect errors | `2>`                  |           |
| Redirect both   | `&>` or `> file 2>&1` |           |
| Ignore output   | `> /dev/null`         |           |
| Ignore errors   | `2> /dev/null`        |           |
| Pipe output     | `command1             | command2` |
| Here-document   | `<<EOF`               |           |
| Here-string     | `<<< "text"`          |           |

---

# 🎉 End of Notes — Redirecting Input & Output (LFCS)

```

---

If you'd like next:

📌 **Shell I/O practice questions + solutions**  
📌 **A full LFCS mock test on shell scripting**  
📌 **Advanced shell redirection: tee, xargs, process substitution**
```
