Below is a **beginner-friendly + LFCS-ready Markdown document** covering the topic:

# **“Compare & Manipulate File Content”**

with examples for:

* `cat`
* `tac`
* `head`
* `tail`
* `sed`
* `cut`
* `uniq`
* `sort`
* `diff`

You can paste this into your GitHub notes.

---

# Compare & Manipulate File Content — LFCS Notes + Examples

This section covers essential Linux commands used to **view**, **filter**, **edit**, **sort**, and **compare** file contents.  
These commands are frequently used in the **LFCS exam**.

---

# =========================================
# 🟦 1. Viewing File Content
# =========================================

## ✅ 1.1 `cat` — Display file content
```bash
cat file.txt
````

Show line numbers:

```bash
cat -n file.txt
```

Concatenate multiple files:

```bash
cat file1 file2 > combined.txt
```

---

## ✅ 1.2 `tac` — Display file content **in reverse**

```bash
tac logfile.txt
```

Output:

```
last line
middle line
first line
```

Useful for reading logs from newest to oldest.

---

# =========================================

# 🟩 2. View Top/Bottom Lines of Files

# =========================================

## ✅ 2.1 `head` — Show first lines

Default (10 lines):

```bash
head file.txt
```

Show first 5 lines:

```bash
head -n 5 file.txt
```

Show first 20 bytes:

```bash
head -c 20 file.txt
```

---

## ✅ 2.2 `tail` — Show last lines

Default:

```bash
tail file.txt
```

Show last 15 lines:

```bash
tail -n 15 file.txt
```

Follow log output in real time (VERY USEFUL):

```bash
tail -f /var/log/messages
```

---

# =========================================

# 🟨 3. Searching / Editing Streams with `sed`

# =========================================

## `sed` = stream editor

Used for replacing, deleting, inserting, and printing lines.

### ✅ 3.1 Replace text (substitution)

```bash
sed 's/error/warning/' file.txt
```

Replace only first occurrence per line.

### Replace globally in each line:

```bash
sed 's/error/warning/g' file.txt
```

### Save output to file:

```bash
sed 's/foo/bar/g' input.txt > output.txt
```

---

### ✅ 3.2 Delete lines

Delete lines containing "debug":

```bash
sed '/debug/d' file.txt
```

Delete line number 3:

```bash
sed '3d' file.txt
```

Delete line range 3–6:

```bash
sed '3,6d' file.txt
```

---

### ✅ 3.3 Print only matching lines (like grep)

```bash
sed -n '/ERROR/p' logfile.txt
```

---

# =========================================

# 🟧 4. Extracting Fields with `cut`

# =========================================

### File Example: `/etc/passwd`

```
root:x:0:0:root:/root:/bin/bash
user:x:1000:1000::/home/user:/bin/bash
```

---

## ✅ 4.1 Cut by delimiter

Show username and shell:

```bash
cut -d':' -f1,7 /etc/passwd
```

Breakdown:

* `-d ':'` → delimiter is colon
* `-f1,7` → fields 1 and 7

---

## ✅ 4.2 Cut by character position

Show only first 5 characters:

```bash
cut -c1-5 names.txt
```

---

## ❗ Tip: Use TAB as delimiter

You must embed a tab using `Ctrl+V + TAB`:

```bash
cut -d'	' -f2 file.txt
```

---

# =========================================

# 🟪 5. Find Duplicate/Unique Lines — `uniq`

Requires **sorted input** to work properly.

---

## Example file:

```
apple
banana
banana
banana
cherry
```

---

## ✅ 5.1 Remove duplicate lines (show unique only)

```bash
uniq fruits.txt
```

---

## ✅ 5.2 Show only duplicate lines

```bash
uniq -d fruits.txt
```

Output:

```
banana
```

---

## ✅ 5.3 Count occurrences

```bash
uniq -c fruits.txt
```

Output:

```
1 apple
3 banana
1 cherry
```

---

# =========================================

# 🟥 6. Sorting File Content — `sort`

---

## Example:

```
john
alice
bob
```

---

## ✔ Alphabetical sort

```bash
sort names.txt
```

---

## ✔ Reverse sort

```bash
sort -r names.txt
```

---

## ✔ Sort numerically

```bash
sort -n numbers.txt
```

File:

```
5
20
3
```

Sorted output:

```
3
5
20
```

---

## ✔ Sort by specific field (CSV)

Sort by 3rd column:

```bash
sort -t',' -k3 employees.csv
```

---

## ✔ Remove duplicates while sorting

```bash
sort -u items.txt
```

---

# =========================================

# 🟫 7. Comparing Files — `diff`

---

## ✔ Compare two files line by line

```bash
diff file1.txt file2.txt
```

---

### Example output interpretation:

```
< old line
> new line
```

Meaning:

* `<` lines exist in file1
* `>` lines exist in file2

---

## ✔ Side-by-side comparison

```bash
diff -y file1.txt file2.txt
```

---

## ✔ Ignore whitespace differences

```bash
diff -w file1.txt file2.txt
```

---

# =========================================

# 🟦 8. Combined Example (LFCS-Style Tasks)

---

## ❓ Task 1

Show only the first 10 lines of `/var/log/messages` that contain "error".

### ✔ Solution:

```bash
grep error /var/log/messages | head
```

---

## ❓ Task 2

Display only usernames from `/etc/passwd`.

### ✔ Solution:

```bash
cut -d':' -f1 /etc/passwd
```

---

## ❓ Task 3

Sort `/etc/passwd` by UID (third field).

### ✔ Solution:

```bash
sort -t':' -k3 -n /etc/passwd
```

---

## ❓ Task 4

Replace the word "failed" with "warning" inside `log.txt`.

### ✔ Solution:

```bash
sed -i 's/failed/warning/g' log.txt
```

---

## ❓ Task 5

Compare two configuration files and highlight the difference.

### ✔ Solution:

```bash
diff -y config.old config.new
```

---

## ❓ Task 6

Show only unique IP addresses in `access.log`.

### ✔ Solution:

```bash
cut -d' ' -f1 access.log | sort | uniq
```

---

## ❓ Task 7

Show last 20 lines of a log file and keep monitoring it.

### ✔ Solution:

```bash
tail -n 20 -f /var/log/secure
```

---

