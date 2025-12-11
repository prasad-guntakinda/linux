Below is a **clean, LFCS-ready Markdown document** that covers **Archive, Backup, Compress, Unpack, and Uncompress files** using the most important Linux tools:

* `tar`
* `gzip`, `gunzip`
* `zip`, `unzip`
* `xz`, `unxz`
* `cpio` (optional LFCS)

All examples are practical and beginner-friendly.

---

````markdown
# Archive, Backup, Compress, Unpack & Uncompress Files — LFCS Notes + Examples

Linux provides many tools for creating archives and compressing files.  
This guide covers the most commonly used commands:

- **tar** → archive + compress  
- **gzip/gunzip** → compress single files  
- **zip/unzip** → common Windows-compatible archive  
- **xz/unxz** → high compression  
- **cpio** → backup tool (LFCS optional)

---

# ============================================
# 1️⃣ tar — Create Archives (With and Without Compression)
# ============================================

`tar` = **tape archive**, used to bundle multiple files/directories into a single file.

---

## 🟦 Common tar options

| Option | Meaning |
|--------|----------|
| `c` | create archive |
| `x` | extract archive |
| `t` | list archive contents |
| `f` | specify filename |
| `v` | verbose output |
| `z` | gzip compression |
| `j` | bzip2 compression |
| `J` | xz compression |

---

# 🟦 1.1 Create a tar archive (no compression)
```bash
tar cf backup.tar /home/user/docs
````

---

# 🟦 1.2 Create a compressed tar archive (.tar.gz)

```bash
tar czf backup.tar.gz /home/user/docs
```

---

# 🟦 1.3 Create tar.xz (high compression)

```bash
tar cJf backup.tar.xz /home/user/docs
```

---

# 🟦 1.4 Extract a tar archive

```bash
tar xf backup.tar
```

Extract tar.gz:

```bash
tar xzf backup.tar.gz
```

Extract tar.xz:

```bash
tar xJf backup.tar.xz
```

---

# 🟦 1.5 List contents without extracting

```bash
tar tf backup.tar.gz
```

---

# 🟦 1.6 Add a file to existing tar archive (no compression)

```bash
tar rf backup.tar newfile.txt
```

---

# 🟦 1.7 Extract to a specific directory

```bash
tar xzf backup.tar.gz -C /tmp/extract_here
```

---

# ============================================

# 2️⃣ gzip / gunzip — Compress Single Files

# ============================================

`gzip` compresses **individual files** (not multiple).
Archive first → then compress using tar.gz.

---

## 🟩 2.1 Compress a file

```bash
gzip file.txt
```

Result:

```
file.txt.gz
```

---

## 🟩 2.2 Decompress

```bash
gunzip file.txt.gz
```

or

```bash
gzip -d file.txt.gz
```

---

## 🟩 2.3 Keep original file while compressing

```bash
gzip -c file.txt > file.txt.gz
```

---

## 🟩 2.4 Check compression ratio

```bash
gzip -l file.txt.gz
```

---

# ============================================

# 3️⃣ zip / unzip — ZIP Archive Format

# ============================================

ZIP files are common on Windows and shared systems.

---

# 🟧 3.1 Create a zip archive

```bash
zip archive.zip file1 file2 file3
```

---

# 🟧 3.2 Zip a directory

```bash
zip -r backup.zip /home/user/docs
```

---

# 🟧 3.3 Unzip an archive

```bash
unzip backup.zip
```

Extract to specific directory:

```bash
unzip backup.zip -d /tmp/myfolder
```

---

# 🟧 3.4 List zip contents

```bash
unzip -l backup.zip
```

---

# ============================================

# 4️⃣ xz / unxz — High Compression

# ============================================

`xz` provides higher compression than gzip.

---

## 🟪 4.1 Compress a file

```bash
xz file.txt
```

Result:

```
file.txt.xz
```

---

## 🟪 4.2 Decompress

```bash
unxz file.txt.xz
```

or

```bash
xz -d file.txt.xz
```

---

## 🟪 4.3 Keep original file

```bash
xz -k file.txt
```

---

## 🟪 4.4 Compress with max compression (slow)

```bash
xz -9 file.txt
```

---

# ============================================

# 5️⃣ cpio — Backup & Archive Tool (Optional LFCS)

# ============================================

Used with `find`, `ls`, or directory pipelines.

---

## 🟫 5.1 Create an archive

```bash
find /home/user | cpio -ov > backup.cpio
```

---

## 🟫 5.2 Extract archive

```bash
cpio -id < backup.cpio
```

---

## 🟫 5.3 List cpio archive contents

```bash
cpio -it < backup.cpio
```

---

# ============================================

# 6️⃣ Combined Real-World Examples (LFCS-Level)

# ============================================

---

## 🌟 Example 1: Archive + Compress Entire /etc Directory

```bash
tar czf etc-backup.tar.gz /etc
```

---

## 🌟 Example 2: Extract at a custom location

```bash
tar xzf etc-backup.tar.gz -C /opt/configs
```

---

## 🌟 Example 3: Backup logs older than 7 days

```bash
find /var/log -mtime +7 -print | tar czf oldlogs.tar.gz -T -
```

---

## 🌟 Example 4: Zip all `.conf` files

```bash
zip conf-files.zip *.conf
```

---

## 🌟 Example 5: Compress all text files with gzip

```bash
gzip *.txt
```

---

## 🌟 Example 6: Unzip into /tmp

```bash
unzip project.zip -d /tmp/project
```

---

## 🌟 Example 7: Create a tar.xz archive for maximum compression

```bash
tar cJf backup.tar.xz /home/user
```

---

## 🌟 Example 8: Extract a single file from tar archive

```bash
tar xf backup.tar file1.txt
```

---

# ============================================

# 7️⃣ Summary Table (Very Important for LFCS)

# ============================================

| Purpose                | Tool       | Example                      |                          |
| ---------------------- | ---------- | ---------------------------- | ------------------------ |
| Archive only           | `tar`      | `tar cf files.tar dir/`      |                          |
| Archive + gzip         | `tar -czf` | `tar czf backup.tar.gz dir/` |                          |
| Compress single file   | `gzip`     | `gzip file.txt`              |                          |
| Decompress             | `gunzip`   | `gunzip file.txt.gz`         |                          |
| ZIP archive            | `zip`      | `zip -r backup.zip /dir`     |                          |
| Unzip                  | `unzip`    | `unzip backup.zip`           |                          |
| High compression       | `xz`       | `xz file.txt`                |                          |
| Restore xz file        | `unxz`     | `unxz file.txt.xz`           |                          |
| Copy via piped archive | `cpio`     | `find .                      | cpio -ov > archive.cpio` |

---

# 🎉 End of LFCS Notes — Archive, Backup & Compression

```

---

If you'd like, I can also provide:

📌 **30 practice questions with solutions**  
📌 **A backup & restore lab similar to LFCS exams**  
📌 **A tar/gzip/zip/xz cheat sheet**
```
