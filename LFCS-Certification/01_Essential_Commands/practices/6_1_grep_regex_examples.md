
# GREP with Basic & Extended Regular Expressions (BRE vs ERE) — LFCS Notes + Examples

`grep` uses **Basic Regular Expressions (BRE)** by default.  
To use **Extended Regular Expressions (ERE)**, you must use:

```

grep -E

```
or  
```

egrep

````

---

# ========================================
# 1️⃣ BASIC REGULAR EXPRESSIONS (BRE)
# ========================================

BRE supports:

- `.` (any character)
- `[]` character classes  
- `[^]` negated classes  
- `*` (zero or more repetitions)
- `^` (start of line)
- `$` (end of line)
- Escape needed for `+`, `?`, `|`, `()` in BRE

---

# 🟦 1.1 Match lines starting or ending with patterns

### ✔ Start of line
```bash
grep "^ERROR" logfile
````

Matches:

```
ERROR initializing system
```

### ✔ End of line

```bash
grep "success$" logfile
```

---

# 🟦 1.2 Match any single character using `.`

```bash
grep "f..d" words.txt
```

Matches:

* feed
* ford
* food
  (any character in place of `..`)

---

# 🟦 1.3 Character classes `[]`

```bash
grep "[aeiou]" file.txt
```

Matches any vowel.

Match digits:

```bash
grep "[0-9]" file.txt
```

Match lowercase letters:

```bash
grep "[a-z]" file.txt
```

Negated class (NOT digits):

```bash
grep "[^0-9]" file.txt
```

---

# 🟦 1.4 Zero or more occurrences (`*`)

```bash
grep "ab*c" file.txt
```

Matches:

* ac
* abc
* abbc
* abbbc
  (0 or more `b`s allowed)

---

# 🟦 1.5 Escaping meta-characters (important in BRE)

### BRE requires escaping for:

* `+`
* `?`
* `|`
* `()`

Examples:

Match one or more digits (`+` must be escaped):

```bash
grep "[0-9]\+" file.txt
```

Match optional character (`?` must be escaped):

```bash
grep "colou\?r" words.txt
```

Matches:

```
color
colour
```

Alternation (OR) must escape `|`:

```bash
grep "cat\|dog" animals.txt
```

Grouping (escaped):

```bash
grep "\(test\)\+" file.txt
```

---

# ========================================

# 2️⃣ EXTENDED REGULAR EXPRESSIONS (ERE)

# ========================================

Use:

```bash
grep -E "pattern"
```

ERE supports more regex features **without escaping**:

* `+` (one or more)
* `?` (zero or one)
* `{n,m}` (quantifiers)
* `|` (alternation)
* `()` grouping

---

# 🟩 2.1 One or more occurrences (`+`)

```bash
grep -E "[0-9]+"
```

Matches:

* 1
* 123
* 98765

---

# 🟩 2.2 Zero or one (`?`)

```bash
grep -E "colou?r" words.txt
```

Matches:

* color
* colour

---

# 🟩 2.3 Alternation (`|`)

```bash
grep -E "cat|dog" animals.txt
```

Matches any line containing:

* cat
* dog

---

# 🟩 2.4 Grouping with parentheses

```bash
grep -E "(error|fail|warn)" logfile
```

---

# 🟩 2.5 Quantifiers `{n}`, `{n,m}`, `{n,}`

Match exactly 3 digits:

```bash
grep -E "[0-9]{3}" file.txt
```

Match 2–4 letters:

```bash
grep -E "[A-Za-z]{2,4}" file.txt
```

Match 3 or more digits:

```bash
grep -E "[0-9]{3,}"
```

---

# ========================================

# 3️⃣ Practical Examples (Useful for LFCS)

# ========================================

## ✔ Find all IPv4 addresses (simple match)

```bash
grep -E "[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+" access.log
```

---

## ✔ Find all email addresses

```bash
grep -E "[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}" users.txt
```

---

## ✔ Match date format (YYYY-MM-DD)

```bash
grep -E "[0-9]{4}-[0-9]{2}-[0-9]{2}" file.txt
```

---

## ✔ Extract only error or warning lines

```bash
grep -E "ERROR|WARNING" logfile
```

---

## ✔ Match lines containing "sshd" but not "Failed"

```bash
grep "sshd" /var/log/secure | grep -v "Failed"
```

---

## ✔ Match only lines with numbers at start

```bash
grep -E "^[0-9]+" file.txt
```

---

## ✔ Match MAC addresses

```bash
grep -E "([0-9A-Fa-f]{2}:){5}[0-9A-Fa-f]{2}" network.txt
```

---

### BRE equivalent (much harder due to escaping):

```bash
grep "\([0-9A-Fa-f]\{2\}:\)\{5\}[0-9A-Fa-f]\{2\}" network.txt
```

---

# ========================================

# 4️⃣ Differences Between BRE and ERE (Summary)

# ========================================

| Feature           | BRE                    | ERE                 |           |   |
| ----------------- | ---------------------- | ------------------- | --------- | - |
| `+`               | Must escape: `\+`      | Normal: `+`         |           |   |
| `?`               | Must escape: `\?`      | Normal: `?`         |           |   |
| `                 | ` (OR)                 | Must escape: `\|`   | Normal: ` | ` |
| `()` grouping     | Must escape: `\(` `\)` | Normal: `()`        |           |   |
| `{n,m}`           | Same                   | Same                |           |   |
| Regex engine type | basic                  | extended            |           |   |
| Command           | `grep`                 | `grep -E` / `egrep` |           |   |

---

# ========================================

# 5️⃣ LFCS Exam-Style Questions

# ========================================

## ❓ Q1: Show lines starting with "root" in /etc/passwd

```bash
grep "^root" /etc/passwd
```

---

## ❓ Q2: Show lines ending with "/bin/bash" using regex

```bash
grep "/bin/bash$" /etc/passwd
```

---

## ❓ Q3: Show UIDs with exactly three digits (100–999)

```bash
grep -E "^[^:]+:[^:]+:[0-9]{3}:" /etc/passwd
```

---

## ❓ Q4: Match "error", "fail", or "warning" (ERE)

```bash
grep -E "error|fail|warning" logfile
```

---

## ❓ Q5: Find all numbers (one or more digits) in a file

```bash
grep -E "[0-9]+"
```

---

# 🎉 End of GREP Regular Expressions Notes

```

---

