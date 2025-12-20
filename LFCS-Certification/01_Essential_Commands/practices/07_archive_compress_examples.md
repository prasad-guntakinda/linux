 **Archive, Backup, Compress, Unpack, and Uncompress files** using the most important Linux tools:

* `tar`
* `gzip`, `gunzip`
* `zip`, `unzip`
* `xz`, `unxz`
* `bzip2`, `bunzip2`
* `cpio` (optional LFCS)


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

Below is a **clean, LFCS-ready Markdown document** with **examples for `bzip2` and `rsync`** under compression/backup topics.
You can directly paste this into your GitHub notes.

---

````markdown
# Compression & Backup Tools — bzip2 and rsync (LFCS Notes + Examples)

This document covers two important tools used for compression and file synchronization in Linux:

- **bzip2 / bunzip2** → High-compression for single files  
- **rsync** → Efficient backup, sync, mirroring with optional compression

---

# ============================================
# 1️⃣ bzip2 — Compress Files
# ============================================

`bzip2` compresses **single files** using the Burrows–Wheeler algorithm.  
It typically compresses better than gzip but slower.

File extension: `.bz2`

## 🟦 1.1 Compress a File
```bash
bzip2 file.txt
````

Result:

```
file.txt.bz2
```

The original file is **replaced** unless you use the `-k` (keep) option.

---

## 🟦 1.2 Keep Original File While Compressing

```bash
bzip2 -k file.txt
```

Now you get:

```
file.txt
file.txt.bz2
```

---

## 🟦 1.3 Decompress a .bz2 File

Using `bunzip2`:

```bash
bunzip2 file.txt.bz2
```

Or using bzip2:

```bash
bzip2 -d file.txt.bz2
```

---

## 🟦 1.4 Verbose Compression

```bash
bzip2 -v file.txt
```

---

## 🟦 1.5 Compress at Maximum Level

Compression levels range from `-1` (fast) to `-9` (max).

```bash
bzip2 -9 file.txt
```

---

## 🟦 1.6 Test Integrity of a .bz2 File

```bash
bzip2 -t file.txt.bz2
```

---

## 🟦 1.7 Compress Multiple Files Using tar + bzip2

```bash
tar cjvf backup.tar.bz2 /home/user
```

Flags:

* `c` → create
* `j` → use bzip2
* `v` → verbose
* `f` → filename

---

## 🟦 1.8 Extract a tar.bz2 Archive

```bash
tar xjvf backup.tar.bz2
```

---

# ============================================

# 2️⃣ rsync — Backup and Synchronization Tool

# ============================================

`rsync` is one of the most powerful tools for:

* Backups
* Incremental copies
* Remote synchronization
* Efficient transfer (only changes sent)
* Compression during transfer

Widely used in LFCS exam scenarios.

---

# 🟩 2.1 Basic rsync Local Copy

```bash
rsync -av /source/ /destination/
```

Options:

* `-a` → archive mode (preserves permissions, timestamps, links, etc.)
* `-v` → verbose

Trailing slash matters:

* `/source/` → copy **contents** of directory
* `/source` → copy directory itself

---

# 🟩 2.2 Backup to a Remote Server (Over SSH)

```bash
rsync -av /home/user/ user@server:/backup/user/
```

Uses SSH by default.

---

# 🟩 2.3 Remote → Local Copy

```bash
rsync -av user@server:/var/log/ /local/logs/
```

---

# 🟩 2.4 Use Compression During Transfer (`-z`)

Useful for slow network links.

```bash
rsync -avz /home/user/ user@server:/backup/
```

`-z` compresses data during transfer.

---

# 🟩 2.5 Delete Files on Destination That Were Deleted at Source

(sync exact mirror)

```bash
rsync -av --delete /source/ /destination/
```

⚠ **Be careful** — this deletes extra files on the destination.

---

# 🟩 2.6 Show Progress During Transfer

```bash
rsync -av --progress /data/ /backup/
```

---

# 🟩 2.7 Exclude Certain Files or Patterns

Exclude `.log` files:

```bash
rsync -av --exclude="*.log" /var/www/ /backup/www/
```

Multiple excludes:

```bash
rsync -av --exclude=node_modules --exclude=*.tmp project/ backup/
```

---

# 🟩 2.8 Dry Run (No Changes Made)

Very important for safety:

```bash
rsync -av --dry-run /source/ /destination/
```

---

# 🟩 2.9 rsync Over a Non-Standard SSH Port

```bash
rsync -av -e "ssh -p 2222" /data/ user@server:/backup/
```

---

# 🟩 2.10 rsync Creating Incremental Backups

```bash
rsync -av --link-dest=/backup/prev/ /data/ /backup/current/
```

Used for snapshot-style backups.

---

# ============================================

# 3️⃣ LFCS Practical Examples

# ============================================

### ✔ Example 1: Compress `/etc` using tar + bzip2

```bash
tar cjvf etc-backup.tar.bz2 /etc
```

---

### ✔ Example 2: Restore the archive

```bash
tar xjvf etc-backup.tar.bz2 -C /tmp/restore
```

---

### ✔ Example 3: Sync `/var/www` to backup server

```bash
rsync -avz /var/www/ admin@192.168.1.50:/backup/www/
```

---

### ✔ Example 4: Mirror `/home` exactly

```bash
rsync -av --delete /home/ /backup/home/
```

---

### ✔ Example 5: Exclude temporary files while syncing

```bash
rsync -av --exclude="*.tmp" /data/ /backup/data/
```

---

### ✔ Example 6: Dry run before real backup

```bash
rsync -av --dry-run /source/ /dest/
```

---

# ============================================

# Summary Table (Exam-Ready)

# ============================================

| Purpose                         | Command                                  |
| ------------------------------- | ---------------------------------------- |
| Compress file                   | `bzip2 file`                             |
| Decompress                      | `bunzip2 file.bz2`                       |
| Keep original while compressing | `bzip2 -k file`                          |
| tar + bzip2 archive             | `tar cjvf archive.tar.bz2 dir/`          |
| extract tar.bz2                 | `tar xjvf archive.tar.bz2`               |
| rsync local copy                | `rsync -av src/ dest/`                   |
| rsync remote copy               | `rsync -av src/ user@host:dest/`         |
| rsync with compression          | `rsync -avz src/ dest/`                  |
| delete missing files            | `rsync -av --delete src/ dest/`          |
| exclude pattern                 | `rsync -av --exclude="*.log" src/ dest/` |
| simulate run                    | `rsync -av --dry-run src/ dest/`         |

---

# 🎉 End of Notes — bzip2 & rsync for LFCS

```

---

If you want, I can also generate:

📌 **30 LFCS backup/compression practice questions**  
📌 **Real-world rsync backup scripts**  
📌 **tar/gzip/bzip2/xz cheat sheet**
```

