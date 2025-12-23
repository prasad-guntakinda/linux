
# 🐧 LFCS – Chapter 1: Essential Commands

## 📁 Create, Delete, Copy, Move Files & Directories

```bash
touch file.txt                     # create empty file
mkdir dir1                         # create directory
mkdir -p a/b/c                     # create nested directories

rm file.txt                        # delete file
rm -r dir1                         # delete directory recursively
rm -rf dir1                        # force delete (use carefully)

cp file1 file2                     # copy file
cp -r dir1 dir2                    # copy directory recursively

mv file1 dir1/                     # move file
mv oldname newname                 # rename file/directory
```

---

## 🔗 Hard Links & Soft Links

```bash
ln file1 hardlink1                 # create hard link
ln -s file1 softlink1              # create symbolic (soft) link

ls -li                             # check inode numbers
```

📌 Notes:

* Hard link → same inode, same filesystem
* Soft link → separate inode, can cross FS

---

## 🔐 File Permissions, SUID, SGID, Sticky Bit

```bash
chmod 755 file                     # rwx r-x r-x
chmod u+x file                     # add execute for owner
chmod g-w file                     # remove write from group

chown user:group file              # change owner & group
chgrp group file                   # change group
```

### Special Permissions

```bash
chmod u+s file                     # SUID (run as owner)
chmod g+s dir                      # SGID (inherit group)
chmod +t dir                       # Sticky bit (shared dirs)
```

```bash
ls -l                              # check s, t flags
```

---

## 🔍 Search for Files (find)

```bash
find /path -name file.txt          # search by name
find / -type f -size +10M          # files larger than 10MB
find /var -perm -u=s               # files with SUID
find . -mtime -1                   # modified in last 1 day
```

```bash
find . -name "*.log" -exec rm {} \;  # execute command on results
```

---

## 📖 Pagers & VI (Basic Demo)

```bash
less file.txt                      # pager (preferred)
more file.txt                      # basic pager
```

### vi Essentials

```bash
vi file.txt                        # open file
i                                 # insert mode
ESC                               # command mode
:w                                # save
:q                                # quit
:wq                               # save & quit
:q!                               # quit without saving
```

---

## 🧾 Compare & Manipulate File Content

```bash
diff file1 file2                   # compare files
sdiff file1 file2                  # side-by-side diff

sort file.txt                      # sort content
uniq file.txt                      # remove duplicates
sort file.txt | uniq               # proper unique output

cut -d: -f1 /etc/passwd            # extract column
wc -l file.txt                     # count lines
```

---

## 🔎 Search Files with grep (Regex)

```bash
grep "error" file.txt              # basic search
grep -i "error" file.txt           # case-insensitive
grep -r "error" /var/log           # recursive search
```

### Extended Regex

```bash
grep -E "err|fail" file.txt        # OR condition
grep -E "^root" /etc/passwd        # starts with root
grep -E "bash$" /etc/passwd        # ends with bash
```

---

## 📦 Archive, Backup, Compress

```bash
tar -cvf backup.tar dir/           # create archive
tar -tvf backup.tar                # list contents
tar -xvf backup.tar                # extract archive
```

### Compression

```bash
tar -czvf backup.tar.gz dir/       # gzip
tar -xzvf backup.tar.gz            # extract gzip

tar -cjvf backup.tar.bz2 dir/      # bzip2
tar -xjvf backup.tar.bz2           # extract bzip2
```

```bash
gzip file                          # compress file
gunzip file.gz                     # decompress
```

---

## 🔁 Input / Output Redirection

```bash
cmd > out.txt                      # stdout overwrite
cmd >> out.txt                     # stdout append
cmd 2> err.txt                     # stderr redirect
cmd > out.txt 2>&1                 # stdout + stderr
cmd1 | cmd2                        # pipe output
```

Example:

```bash
ls /nope 2> error.log              # save errors
```

---

## 🌱 Git Operations (Basics)

```bash
git init                           # initialize repo
git status                         # check status
git add file.txt                   # stage file
git commit -m "msg"                # commit changes
```

```bash
git log                            # commit history
git diff                           # unstaged changes
git clone repo_url                 # clone repository
```

---

## 🔐 Work with SSL Certificates (Quick)

```bash
openssl genrsa -out server.key 2048    # generate private key
chmod 600 server.key                  # secure key

openssl req -x509 -new -nodes \
 -key server.key -days 365 -out server.crt
# create self-signed cert
```

```bash
openssl x509 -in server.crt -noout -text   # view cert
```

📌 Standard paths (Ubuntu):

```text
/etc/ssl/certs/      # certificates
/etc/ssl/private/    # private keys
```

---

## 🎯 LFCS EXAM QUICK TIPS (Chapter-1)

✔ Prefer **find + grep** combo
✔ Use **tar** for backup questions
✔ ACL if chmod is not enough
✔ Redirect errors explicitly (`2>`)
✔ Always **verify output**

---

If you want next:

* 📄 **Printable PDF of all cheat sheets**
* ⏱ **Chapter-1 timed mock (30 mins)**
* 🧪 **“Fix the broken command” scenarios**

Just say 👉 **“Next mock”** 🚀
