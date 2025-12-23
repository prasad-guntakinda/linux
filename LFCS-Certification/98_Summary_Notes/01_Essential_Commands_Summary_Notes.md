# 01_Essential Commands Summary Notes:


        - create, delete,copy and move files and directories
        - create and manage hard links and soft links
        - File Permissions, SUID, SGID, Sticky Bit
        - Search for files(find), Pagers and VI Demo
        - Compare and Manipulate File Content
        - Search file by grep, basic and extended regular expressions
        - Archive, backup, compress, uncompress files
        - Input-Output redirection (>, >>, |, 2>)
        - Git Operations
        - Work with SSL certs


## 1.1 Files and Directories

### Basic Commands
- __commands:__ `ls, pwd, mkdir -p, cp, mv,rm`

### Change Ownership: chgrp, chown

- Only the owner of a file or directory can change permissions, except root user
- We can only change to groups that our user is part of. We can see to what groups our current user belongs with `groups` command
- Again, the root user is the exception, which can change the group of a file or directory to whatever group exists on the system.


#### chgrp
- `chgrp = CHange GRouP` It allows you to change only the group ownership of a file or directory.

- If you need to change BOTH owner and group → use `chown user:group file`.

```bash
#Syntax:
chgrp [OPTION]... GROUP FILE...
chgrp [OPTION]... --reference=RFILE FILE...
chgrp group_name file/directory

chgrp friends family_dog.jpg
#when updating a whole directory tree
sudo chgrp -R webadmins /var/www
#using GID
sudo chgrp 1002 report.log
sudo chgrp syslog *.log
sudo chgrp -h users symlink_name
# --reference
#`config.ini` will take the same group as `/etc/passwd`.
sudo chgrp --reference=/etc/passwd config.ini
#Linux does **not** allow removing group ownership entirely (unlike ACLs).
#But you can change it to the user’s primary group:
sudo chgrp $(id -gn alice) file.txt
```

#### chown:
- To change the owner of a file or directory: `chown` (change owner).
- `chown` allows you to change `file owner`, `file group` and `both owner and group`

```bash
 #change owner and group for a file
 chown OWNER:GROUP FILE
 # copy the ownership from reference file
 chown --reference=RFILE FILE
 # owner change to alice but group ownership stays the same
 sudo chown alice file.txt
 # only group changes to developers and owner remains unchanged
 sudo chown :developers file.txt
 
 #Change Both Owner AND Group
 sudo chown alice:developers file.txt
 #Change Ownership of a Directory Recursively
 sudo chown -R alice:developers /project

 #Give Ownership of a Folder to root
 sudo chown root:root /secure/logs
 #Change Ownership to a Numeric UID and GID
 sudo chown 1001:1002 file.txt

 #Use `--reference`:
 #Owner and group of `config.txt` will match the owner/group of `/etc/passwd`.
 sudo chown --reference=/etc/passwd config.txt

 #Change Ownership Only If File is a Symbolic Link Target
 sudo chown -h alice linkname

 #Remove Group Ownership (reset to default group of owner)
 #Group becomes the primary group of user `alice`.
 sudo chown alice: file.txt
```

---

### Change Permissions: chmod
#### -rwxrwxrwx
- First char `-` is shows what type of entry it is:

```
- : regular file
d : directory
c : character device
l : link
s : socket file
p : pipe
b : block device
```
- The next 9 characters show us permissions:

![File Permissions](./images/file_permissions_2.png)
- __First 3:__ permissions for the user that owns this file.
- __Next 3:__ permissions for the group of this file.
- __Last 3:__ permissions for other users (any user that is not a owner or not part of the group).

```bash
# -rwxrwxrwx

```

#### What permission means on File?

- `-` means no permission they cannot read/write/execute it.
- `r` read the contents of this file.
- `w` write to this file, modify its contents.
- `x` execute this file. Some files can be programs or shell scripts (instructions we can execute).

#### What permission means on a directory?

- `-` means no permission they cannot view/read/write/execute it.
- `r (read)` Allows viewing the contents of the directory (listing files and subdirectories).To run a command like `ls Pictures/` 

- `w (write)` Allows creating, deleting, and renaming files and subdirectories within the directory. add/delete files or subdirectories as when we use `mkdir`.

- `x (execute)` Allows entering (traversing) the directory and accessing its contents, and executing files within it if those files also have execute permission. We need `x` to be able to do `cd Pictures/` 

- For a user to be able to enter a directory and execute commands within it then the directory must have at least `r-x` permissions for that user.
- If user has `rw-` can user enter into the dir and execute commands?
- Without `x`, the user cannot `cd` into the directory or access anything inside it, even if they have read/write permissions on the contents.

---

#### chmod

- `chmod = CHange MODe (permissions)` It modifies file/directory permissions for:

```
u : User
g : Group
o : Others
a : All
```
#### Symbolic mode → chmod u+r file
- To add permissions use:

```
user    u+  u+r/u+rw/u+rwx

group   g+  g+r/g+rw/g+rwx

others  o+  o+r/o+rw/o+rwx

all     a+  a+r/a+rw/a+rwx

Chaining:
------
chmod u+x, g+w, o+r filename
chmod ug+x filename
chmod go+r filename
```   

- To remove permissions use:

```
user    u-  u-r/u-rw/u-rwx

group   g-  g-r/g-rw/g-rwx

others  o-  o-r/o-rw/o-rwx

all     a-  a-r/a-rw/a-rwx

Chaining:
---------
chmod u-x, g-w, o-r filename
chmod ug+x, o-x filename
chmod go-r filename
```

- Setting exact permissions

```bash
chmod u=rx g=r o=r filename
chmod g=rw filename
# disable all permissions
chmod o= filename
#or
chmod o-rwx filename
#Chaining
chmod u+x,g=rw,o= filename
```
####  Numeric(Octal) mode → chmod 755 file

![Octal Permissions](./images/octal_permissions.png)

```
read    : 4
write   : 2
execute : 1
-       : 0
```

##### Examples

```bash
chmod 640 family_dog.jpg
# same as
chmod u=rw-,g=r family_dog.jpg
# Make a directory accessible to everyone
chmod 777 /tmp/share
# Apply permissions to directories + all files inside.
# group read/write to whole directory
chmod -R g+rw /data
# Remove other permissions recursively
chmod -R o-rwx /secure
# implicitly uses chmod a+x script.sh
chmod +x script.sh
# same as
chmod a+x script.sh
```

---
### SUID, SGID, Stickt Bit:

Along with normal permissions (r,w,x), Linux has **three special permission bits**:

| Special Bit    | Applies To          | Numeric Value | Symbol      |
| -------------- | ------------------- | ------------- | ----------- |
| **SUID**       | Files               | 4xxx          | `s` (user)  |
| **SGID**       | Files & directories | 2xxx          | `s` (group) |
| **Sticky Bit** | Directories         | 1xxx          | `t`         |

These bits **change how files behave during execution** and **how directories handle file deletion**.

#### SUID:

- When a file has **SUID**, anyone who runs that file temporarily executes it **as the file’s owner**.

- How to set?

```bash
#rwsr-xr-x
chmod 4755 file
chmod u+s file
#Example:
ls -l /usr/bin/passwd
```

#### SGID:

- When a file has SGID: Users execute the file **with the file’s group privileges**. Rarely used on files nowadays (potential security risks).

- All new files created inside the directory **inherit the directory’s group**, not the user’s primary group.
- How to set?

```bash
#rwxr-sr-x
chmod 2755 file
chmod g+s file
```

#### Sticky Bit:

- Sticky Bit allows **only the file’s owner** (or root) to delete files inside a directory — EVEN if the directory is world-writable.

```bash
#The last `t` means sticky bit is set.
#drwxrwxrwt
chmod 1777 directory
chmod +t directory
```

---


# 3. Hard Links and Soft Links: ln,

## How a file is stored in filesystem?

```bash
echo "Picture of Milo the dog" > Pictures/family_dog.jpg
stat Pictures/family_dog.jpg
```
- Filesystems like xfs, ext4, and others, keep track of data with the help of `inodes`. 
- An `inode` is a filesystem data structure that stores metadata about a file (or directory) — everything except the filename and the file’s actual data blocks.

- Our picture might have blocks of data scattered all over the disk, but the inode remembers where all the pieces are stored. It also keeps track of metadata. 

### Key concepts

-  **File name** → stored in directory.The directory entry maps a filename to an inode number

- **Inode** → stores actual file data & metadata (permissions, size, blocks)A file’s identity on disk is the inode; filenames are directory entries that reference it.

- __Hard links:__  A **hard link** is an **additional name** for the same **inode** (same file on disk). 
- __Symbolic links:__ A symlink is a separate file with its own inode that contains a path — it does not share the target’s inode. Creating new object what has a path

- __Per-filesystem:__ Inodes exist per filesystem and inode numbers are unique only within that filesystem. Filesystems allocate a fixed number (or ratio) of inodes when created — you can run out of inodes even if there is free disk space.

```
Filename  → points to → Inode → contains actual file data 
```

```shell
ls -i file.txt        # prints inode number before filename
ls -li               # long listing including inode
stat file.txt        # detailed info including inode on Linux
#Find a file by inode:
find /path -inum 123456 -print
df -i               # shows used/available inodes per filesystem
```

### Hard Links:

- A **hard link** is an **additional name** for the same **inode** (same file on disk).
- Hard link == real file (both point to same inode)
- If you delete original file → hard link still works
- Multiple filenames (directory entries) can reference the same inode. 
- The inode’s link count increases for each hard link. Removing a filename decrements the link count
- The data is freed only when link count reaches `0` and no process has the file open. Same object two references
- Cannot create hard links to:
    - Directories
    - Files on different filesystems/partitions

#### How to create hard links?

```bash
echo "hello world" > file1.txt
# create hard link
ln file1.txt file1_hard.txt
# check inode numbers + link count
# Both have the same inode number. This means they are the same file with two different names
ls -li file1.txt file1_hard.txt
stat file1.txt
```
- It will be created as regular file and file type is regular `-` not `l`

### Soft Links:

- A **soft link** is a **pointer** to another file (like a Windows shortcut).
- Soft link has a **different inode**
-If you delete original file → **soft link breaks**
- Can link to:
    - Directories
    - Files across filesystems

#### How to create soft links?

```bash
ln -s file1.txt file1_soft.txt
# Check inode numbers
ls -li file1.txt file1_soft.txt
# Soft Link Breaks if Target is Removed
rm file1.txt
cat file1_soft.txt
# soft link to a directory
ln -s /var/log logs_link
```

- Different `inode` numbers
- Arrow (`→`) shows the link target
- Soft link stores **path**, not file data


### Summary:

| Feature                     | Hard Link   | Soft Link (Symbolic) |
| --------------------------- | ----------- | -------------------- |
| Points to                   | inode       | filename/path        |
| Inode number                | same        | different            |
| Works across filesystems    | ❌ No        | ✔ Yes                |
| Works with directories      | ❌ No        | ✔ Yes                |
| Breaks if original deleted? | ❌ No        | ✔ Yes                |
| Acts like the original file | ✔ Yes       | ❌ No                 |
| Can identify target         | No easy way | `→` path visible     |


---

# 3. Searching for Files: find

- __find:__ search for files in a directory hierarchy

- __syntax:__

```bash
find [path...] [options] [expression]

```
 
- The  command recursively searches the directory tree starting from the specified `[path]`  and locates files based on the criteria defined in the `[expression]`

## Key Components 

- `[path...]:` Specifies the starting directory or directories for the search. 

    - `.` searches the current directory and its subdirectories. 
	- `/` searches the entire filesystem starting from the root directory. 
	- `~` searches from the current user's home directory. 

- `[options]`: Controls the overall behavior of the search, such as handling of symbolic links (`-L`,`-P` ) or search depth (`-maxdepth`). 
- `[expression]`: This is the core of the command, defining the criteria (tests) for filtering files and the actions to perform on matching files. Multiple expressions can be combined using logical operators like `-and`  (implied if omitted), `-or`, and `!` (not). 

## Common find Expressions 

- Expressions consist of tests and actions: [6]  

| Category | Option | Description | Example  |
| --- | --- | --- | --- |
| __Name__ | `-name` | Searches for files with an exact name match (case-sensitive). | `find . -name "notes.txt"`  |
|  | `-iname` | Case-insensitive name search. | `find /home -iname "steve*"`  |
| __Type__ | `-type` | Filters by file type. | `find /var -type d` (directories)  |
| — | `f`,`d` ,`l` | Descriptors for regular file, directory, and symbolic link, respectively. | `find . -type f` (regular files)  |
| __Size__ | `-size` | Finds files by size. Use `+` for greater than, `-` for less than. | `find /path -size +100M` (greater than 100MB)  |
| — | `c`, `k`, `M`, `G` | Suffixes for bytes, kilobytes, megabytes, and gigabytes. | `find . -size -1k` (less than 1kB)  |
| __Time__ | `-mtime` | Finds files modified within/more than `n` days ago. | `find . -mtime -7` (modified within the last 7 days)  |
|  | `-mmin` | Finds files modified within/more than `n` minutes ago. | `ind . -mmin -60` (modified within the last hour)  |
| __Ownership__ | `-user` | Finds files owned by a specific user. | `find / -user syslog`  |
|  | `-group` | Finds files owned by a specific group. | `find . -group staff`  |
| __Permissions__ | `-perm` | Finds files with specific permissions. | `find . -perm 644`  |
| __Action__ | `-exec` | Executes a specified command on each matched file. | `find . -name "*.log" -exec rm {} \\;`  |
| — | `-delete` | Deletes the found files or empty directories. | `find . -type f -name "*.tmp" -delete`  |


---


# 4. Compare & Manipulate File Content

- __basic commands:__ `cat`, `tac`, `head`, `tail`, `sed`, `cut`, `uniq`, `sort`, `diff`, 

---

# 5. Pagers and Vim Editor:


        - Vim navigation  
        - Editing commands  
        - Searching & replacing  
        - Copy/paste/delete  
        - Working with multiple files  
        - Pagers (`less`, `more`)

## Vim Cheat Sheet:


Vim operates in **three main modes**:

| Mode | Description | Enter |
|------|-------------|--------|
| **Normal Mode** | navigation, delete, copy, commands | `Esc` |
| **Insert Mode** | typing text | `i`, `a`, `o` |
| **Command Mode** | saving, quitting, settings | `:` |

---

### 1. OPENING & SAVING FILES

| Action | Command |
|--------|---------|
| Open a file | `vim file.txt` |
| Save (write) | `:w` |
| Quit | `:q` |
| Save & quit | `:wq` |
| Quit without saving | `:q!` |
| Save as another name | `:w newfile.txt` |

---

### 2. INSERT MODE COMMANDS

| Command | Meaning |
|---------|----------|
| `i` | Insert text at cursor |
| `I` | Insert at start of line |
| `a` | Append text after cursor |
| `A` | Append at end of line |
| `o` | Open new line **below** |
| `O` | Open new line **above** |

- To exit Insert Mode use `Esc`

---
### 3. MOVEMENT / NAVIGATION

| Keys | Action |
|------|---------|
| `h` | left |
| `j` | down |
| `k` | up |
| `l` | right |
| `0` | beginning of line |
| `$` | end of line |
| `w` | next word |
| `b` | previous word |
| `gg` | go to top of file |
| `G` | go to bottom |
| `:N` | go to line N (example: `:25`) |

---

### 4. DELETING TEXT

| Command | Meaning |
|---------|----------|
| `x` | delete character |
| `dd` | delete whole line |
| `5dd` | delete 5 lines |
| `dw` | delete word |
| `D` | delete to end of line |

---

### 5. COPY & PASTE (YANK & PUT)

| Command | Meaning |
|---------|----------|
| `yy` | copy (yank) line |
| `5yy` | copy 5 lines |
| `p` | paste below cursor |
| `P` | paste above cursor |

---

### 6. UNDO / REDO

| Command | Action |
|---------|---------|
| `u` | undo |
| `Ctrl + r` | redo |

---

### 7. SEARCHING INSIDE VIM

Search for “error”:
```

/error

Next match: n
Previous match:N
Case-insensitive: /error\c
Search backwards: ``?
```


---

### 8. SEARCH & REPLACE (IMPORTANT FOR LFCS)

Replace first occurrence per line:

```
:%s/old/new/
```

Replace globally in each line:

```
:%s/old/new/g
```

Replace with confirmation:

```
:%s/old/new/gc
```

Replace only on specific lines:

```
:10,20s/error/warning/g
```

---

### 9. VISUAL MODE (SELECTING TEXT)

| Mode                | Command    |
| ------------------- | ---------- |
| Character selection | `v`        |
| Line selection      | `V`        |
| Block selection     | `Ctrl + v` |

Actions after selection:

* `y` → copy
* `d` → delete
* `p` → paste

---

### 10. MULTIPLE FILES / BUFFERS

Open multiple:

```bash
vim file1 file2 file3
```

Switch buffers:

```
:bn   # next buffer
:bp   # previous buffer
:ls   # list open buffers
```

Open a new file inside Vim:

```
:e newfile
```

---

## Pagers: less, more

Pagers help view large text files **page-by-page**.

### 1. `less` (most powerful)

Open file:

```bash
less /var/log/messages
```

#### Navigation:

| Action        | Key                 |
| ------------- | ------------------- |
| Down / Up     | `j` / `k`           |
| Page down     | `space` or PageDown |
| Page up       | `b`                 |
| Start of file | `g`                 |
| End of file   | `G`                 |

#### Searching:

Search for "error":

```
/error
Next match: n
Previous match:N
Quit:q
```

#### View long output pipe:

```bash
dmesg | less
```

---

### 2. `more` (basic pager)

Open a file:

```bash
more textfile
```

### Navigation:

| Action    | Key     |
| --------- | ------- |
| Next page | `space` |
| Next line | `Enter` |
| Search    | `/word` |
| Quit      | `q`     |

---

## When to Use Which?

| Task                   | Recommended Tool |
| ---------------------- | ---------------- |
| Large log files        | **less**         |
| Searching inside files | **less**         |
| Quick view             | `more` or `less` |
| Viewing piped output   | `less`, `more`   |

---

## QUICK COMMAND SUMMARY

| Task                   | Command                    |
| ---------------------- | -------------------------- |
| Edit config file       | `vim /etc/ssh/sshd_config` |
| Search inside file     | `/pattern`                 |
| Replace text in file   | `:%s/old/new/g`            |
| View logs paged        | `less /var/log/messages`   |
| Follow logs            | `tail -f /var/log/secure`  |
| Move to line 55 in Vim | `:55`                      |
| Copy 10 lines          | `10yy`                     |
| Paste                  | `p`                        |

---


# 6. Searching Files usig grep:

## Summary

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

## grep with regular expressions

- `grep` uses **Basic Regular Expressions (BRE)** by default.  
- To use **Extended Regular Expressions (ERE)**, you must use: `grep -E or egrep`
- BRE supports:

- `.` (any character)
- `[]` character classes  
- `[^]` negated classes  
- `*` (zero or more repetitions)
- `^` (start of line)
- `$` (end of line)
- `\+` (one or more repetitions)
* `\?` (optional character)
* `\|` (alternation)
* `\(\)` (grouping)
- Escape needed for `+`, `?`, `|`, `()` in BRE

### Examples

```bash
#Match lines starting or ending with patterns
# lines starting with ERROR
grep "^ERROR" logfile
grep "success$" logfile
# match single char
grep "c.t" words.txt
# cat, cut, cot,cet,..etc 
# matches any vowel
grep "[aeiou]" file.txt
# matches digits
grep "[0-9]" file.txt
# zero or more occurences
grep "ab*c" file.txt
#ac, abc, abbc, abbbc,.. 0 or more `b`s allowed
# Match one or more digits
grep "[0-9]\+" file.txt
#Match optional character
grep "colou\?r" words.txt # color or colour
# alternation
grep "cat\|dog" animals.txt
# grouping
grep "\(test\)\+" file.txt
```

## Extended Regular Expressions:

```bash
grep -E "pattern"
egrep "pattern"
```
- ERE supports more regex features **without escaping**:

* `+` (one or more)
* `?` (zero or one)
* `{n,m}` (quantifiers)
* `|` (alternation)
* `()` grouping

```bash
grep -E "[0-9]+"
grep -E "colou?r" words.txt
grep -E "(error|fail|warn)" logfile
```

### Quantifiers `{n}`, `{n,m}`, `{n,}`

- `{n,m}` `n`: min count, `m`: max count

```bash
#Match exactly 3 digits:
grep -E "[0-9]{3}" file.txt
# Match 2–4 letters:
grep -E "[A-Za-z]{2,4}" file.txt
#Match 3 or more digits:
grep -E "[0-9]{3,}"
```

---


# 7. Archive, Backup, Compress, Unpack and Uncompress files 

- Linux provides many tools for creating archives and compressing files.  
            - tar
            - gzip, gunzip
            - bzip2, bunzip2
            - xz, unxz
            - zip/unzip
            - cpio
            - rsync

## 1. tar: tape archive:

- `tar` = **tape archive**, used to bundle multiple files/directories into a single file.
- Create Archives (With and Without Compression)
### tar options

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


```bash
#Create a tar archive (no compression)
tar cf backup.tar /home/user/docs
# Create a compressed tar archive (.tar.gz)
tar czf backup.tar.gz /home/user/docs
tar cJf backup.tar.xz /home/user/docs

# Extract a tar archive
tar xf backup.tar
#Extract tar.gz
tar xzf backup.tar.gz
#Extract tar.xz
tar xJf backup.tar.xz

#List contents without extracting
tar tf backup.tar.gz

# Add a file to existing tar archive (no compression)
tar rf backup.tar newfile.txt
# Extract to a specific directory
xzf backup.tar.gz -C /tmp/extract_here
```

## 2.gzip, gunzip:

- `gzip` compresses **individual files** (not multiple).
- Archive first → then compress using `tar.gz`.

```bash
#Compress a file
gzip file.txt
# Decompress
gunzip file.txt.gz
# or
gzip -d file.txt.gz
# Keep original file while compressing
gzip -c file.txt > file.txt.gz
```
---

## 3.bzip2, bunzip2
- `bzip2` compresses **single files** using the Burrows–Wheeler algorithm.  
- It typically compresses better than gzip but slower.
- File extension: `.bz2`
- The original file is **replaced** unless you use the `-k` (keep) option.
- Examples

```bash
# compress a file
bzip2 file.txt
# Keep Original File While Compressing
bzip2 -k file.txt
# Decompress a .bz2 File
bunzip2 file.txt.bz2
# or
bzip2 -d file.txt.bz2
# Compress at Maximum Level
# Compression levels range from `-1` (fast) to `-9` (max).
bzip2 -9 file.txt

# Compress Multiple Files Using tar + bzip2
tar cjvf backup.tar.bz2 /home/user
# extract
tar xjvf backup.tar.bz2
```


---
## 4. xz, unxz

- `xz` provides higher compression than gzip.
```bash
#Compress a file
xz file.txt
# Keep original file
xz -k file.txt
#Decompress
unxz file.txt.xz
# or
xz -d file.txt.xz
# Compress with max compression (slow)
xz -9 file.txt

```
--
## 5. zip/unzip
- ZIP files are common on Windows and shared systems.

```bash
zip archive.zip file1 file2 file3
# Zip a directory
zip -r backup.zip /home/user/docs
# Unzip an archive
unzip backup.zip
# Extract to specific directory:
unzip backup.zip -d /tmp/myfolder
# List zip contents
unzip -l backup.zip
```


---
## 6.cpio
- Used with `find`, `ls`, or directory pipelines.

```bash
#Create an archive
find /home/user | cpio -ov > backup.cpio
# Extract archive
cpio -id < backup.cpio
# List cpio archive contents
cpio -it < backup.cpio
```
--
## 7. rsync : Backup and Synchronization Tool

`rsync` is one of the most powerful tools for:

* Backups
* Incremental copies
* Remote synchronization
* Efficient transfer (only changes sent)
* Compression during transfer

Trailing slash matters:

* `/source/` → copy **contents** of directory
* `/source` → copy directory itself

```bash
# Basic rsync Local Copy
rsync -av /source/ /destination/
# `-a` → archive mode (preserves permissions, timestamps, links, etc.)
#`-v` → verbose

# Backup to a Remote Server (Over SSH): Uses SSH by default.
rsync -av /home/user/ user@server:/backup/user/

#Remote → Local Copy
rsync -av user@server:/var/log/ /local/logs/

# Use Compression During Transfer (`-z`). Useful for slow network links.
rsync -avz /home/user/ user@server:/backup/


# Delete Files on Destination That Were Deleted at Source (sync exact mirror). this deletes extra files on the destination.
rsync -av --delete /source/ /destination/

rsync -av --exclude="*.log" /var/www/ /backup/www/
rsync -av --exclude=node_modules --exclude=*.tmp project/ backup/

# rsync Creating Incremental Backups
rsync -av --link-dest=/backup/prev/ /data/ /backup/current/
```

## Summary Table:

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


## bzip2 and rsync summary:

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

# 8. Redirecting input-output: >, >>, |, 1>, 2>

        * Output redirection
        * Input redirection
        * Append vs overwrite
        * Redirecting errors
        * Redirecting both output & errors
        * Pipelines
        * Here-documents
        * Here-strings

Redirecting input/output is a core Linux skill.  
It allows you to send command results into files, combine commands, and control where errors go.

Linux has 3 standard streams:

| Stream | File Descriptor | Description |
|--------|-----------------|-------------|
| STDIN  | 0 | Input to a program |
| STDOUT | 1 | Normal output |
| STDERR | 2 | Error messages |

## Redirecting Output (STDOUT)

- Overwrite a file (`>`)
- Write output to a file, replacing existing content:
```bash
# If `file.txt` existed, it is overwritten.
echo "Hello" > file.txt
```

- Append to a file (`>>`)

```bash
# Adds content to the end of the file.
echo "Hello Again" >> file.txt
```

- Examples:

```bash
#Redirect command output to a file
ls -l > listing.txt
# Append version:
ls -l >> listing.txt
```

## Redirecting Input (STDIN)

- Use `<` to feed a file into a command.
- 

```bash
# Count words in a file
wc -l < file.txt
# Sort using a file as input
sort < names.txt
```

## Redirecting Error Messages (STDERR)

- STDERR uses file descriptor `2`.


```bash
# Redirect errors to a file
ls /nonexistent 2> errors.txt
# Append errors:
ls /nonexistent 2>> errors.txt
```

## Redirecting BOTH Output & Errors

```bash
# Method 1: Redirect each separately
command > output.txt 2> error.txt

# Method 2: Merge STDERR into STDOUT
# `> all.txt` → redirect stdout
# `2>&1` → send stderr to same file
command > all.txt 2>&1

# Method 3: Redirect both STDOUT & STDERR (modern)
command &> file.txt
# Append version:
command &>> file.txt
```

## Pipes (|) — Send output to another command

- Pipes take STDOUT of one command and send it as STDIN to another.

- Examples 

```bash
# View logs with grep
journalctl -xe | grep ssh
# Sort process list by memory usage
ps aux | sort -k4 -nr
# Show top 10 IP addresses from a log
cut -d' ' -f1 access.log | sort | uniq -c | sort -nr | head
# Pipe multiple commands
ls -l | grep "^d" | wc -l
```

## Here-Documents (<<)

- A Here Document (or heredoc) is a type of redirection that allows you to pass multiple lines of input to a command directly from within a script or the command line.

- Basic Syntax
You define a heredoc using the `<<` operator followed by a "delimiter" word (usually `EOF`, `STOP`, or `END`).

```bash
command << DELIMITER
line 1
line 2
line 3
DELIMITER
```

-Example:

```bash
cat << EOF > greetings.txt
Hello Prasad,
Welcome to Linux in 2025.
This file was created using a heredoc.
EOF
```

- __cat:__ The command receiving the input.
- __<< EOF:__ Tells the shell "start reading everything until you see the word EOF".
- __> greetings.txt:__ Redirects the output of cat into a file.

- Example2:

```bash

grep "app" << EOF
banana
mango
app
cherry
EOF

```

### Key Rules and Features:

-  __The Ending Delimiter:__ The closing word (`EOF`) must be on its own line and have no leading or trailing spaces.

- __Variable Expansion:__ By default, variables inside a heredoc are expanded.
```bash
NAME="Prasad"
cat << EOF
User: $NAME
EOF
# Output: User: Prasad
```
- __Disabling Expansion:__ If you wrap the first delimiter in quotes, the shell treats the text as literal (no variables will be expanded).
```bash
cat << "EOF"
The variable $NAME will not print Prasad.
EOF
```

- __Indentation Support (<<-):__ If you are writing a script and want to indent your heredoc for readability, use `<<-`. This tells the shell to ignore leading tabs (but not spaces).


## Here-Strings (<<<)

- Sends a **single string** as input to a command.

```bash
grep oo <<< "food"
wc -w <<< "hello world linux"
```

- __Comparison:__ Heredoc vs. Herestring
- Heredoc (<< EOF): Used for multiple lines.
- Herestring (<<<): Used for a single string or variable.
Example: grep "search" <<< "$long_variable_text"


## Redirecting to /dev/null (Discard Output):

- Useful for ignoring output or errors.


```bash
# Discard normal output
command > /dev/null
# Discard only errors
command 2> /dev/null
# Discard both
command > /dev/null 2>&1

command &> /dev/null

```


## Summary Table:


| Task            | Command               |
| --------------- | --------------------- |
| Redirect output | `>`                   |
| Append output   | `>>`                  |
| Redirect input  | `<`                   |
| Redirect errors | `2>`                  |
| Redirect both   | `&>` or `> file 2>&1` |
| Ignore output   | `> /dev/null`         |
| Ignore errors   | `2> /dev/null`        |
| Pipe output     | `command1 \|command2` | 
| Here-document   | `<<EOF`               |
| Here-string     | `<<< "text"`          |




---


# 9. SSL/TLS certificates

LFCS **DOES test**:
✅ Generate **private keys**
✅ Create **CSR (Certificate Signing Request)**
✅ Create **self-signed certificates**
✅ Configure SSL for a **web server (Apache / Nginx)**
✅ Verify certificates
✅ Understand file locations & permissions


## Key SSL/TLS Components (Minimal Theory)

| Component                     | Purpose                       |
| ----------------------------- | ----------------------------- |
| Private Key (`.key`)          | Secret key, must be protected |
| CSR (`.csr`)                  | Request sent to CA            |
| Certificate (`.crt` / `.pem`) | Public identity               |
| CA                            | Entity that signs cert        |

> 🔑 **Private key must never be world-readable**

## 1. Generate a Private Key

```bash
# generates 2048-bit RSA private key
openssl genrsa -out server.key 2048
# Secure it: # restrict access to owner only
chmod 600 server.key
# Verify Key: validates private key integrity
openssl rsa -in server.key -check

```

## 2. Create a Certificate Signing Request (CSR)

- Method_1: generate private key first and by using it generate CSR

```bash
# extension can be .key/.pem
openssl genrsa -out my_private.key 2048
# generates CSR using existing private key
openssl req -new -key my_private.key -out server.csr

```

- Method_2: Directly generate new private key + CSR request at a time

```bash
# The same but just using req:
openssl req -newkey rsa:2048 -keyout my_private.pem -out my_csr_req.pem
# view CSR contents
openssl req -in my_csr_req.pem -noout -text


```

- During prompts:

* **Common Name (CN)** → hostname (e.g. `www.example.com`)
* Other fields can be minimal for exam


## 3. Create a Self-Signed Certificate 


```bash
# creates self-signed cert with existing private key valid for 1 year
openssl req -x509 -new -nodes \
  -key server.key \
  -sha256 \
  -days 365 \
  -out server.crt

# generates new private key + self-signed certificate
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out req.pem
openssl x509 -in server.crt -noout -text
# view certificate details
```
## 4. Common Certificate Locations (Ubuntu)

| File Type    | Location            |
| ------------ | ------------------- |
| Certificates | `/etc/ssl/certs/`   |
| Private Keys | `/etc/ssl/private/` |

- Move files:

```bash
cp server.crt /etc/ssl/certs/
cp server.key /etc/ssl/private/
chmod 600 /etc/ssl/private/server.key
```
---

## 5. Configure Apache with SSL (Ubuntu)


```bash
# Install Apache & SSL Module
apt update
apt install -y apache2
a2enmod ssl                  # enable SSL module
systemctl restart apache2
# Default SSL Site
/etc/apache2/sites-available/default-ssl.conf
# Enable it 
a2ensite default-ssl
systemctl reload apache2
```

#### Configure Certificate Paths

- create self-signed cert
```bash
openssl req -x509 -newkey rsa:2048 -keyout pr_apache2_pk.key -out pr_apache2_x509_cert.crt
cp pr_apache2_x509_cert.crt  /etc/ssl/certs/
cp pr_apache2_pk.key /etc/ssl/private/
chmod 600 /etc/ssl/private/pr_apache2_pk.key
```
- Edit:

```bash
vi /etc/apache2/sites-available/default-ssl.conf
```

- Set:

```apache
SSLCertificateFile    /etc/ssl/certs/pr_apache2_x509_cert.crt
SSLCertificateKeyFile /etc/ssl/private/pr_apache2_pk.key
```

- Restart:

```bash
systemctl restart apache2
```

---

#### Verify HTTPS

```bash
ss -tuln | grep 443       # confirm HTTPS port
curl -k https://localhost
# -k allows self-signed cert
```
 
---

## 6. Configure Nginx with SSL (Alternative)

- Install:

```bash
apt install -y nginx
# Edit server block:
vi /etc/nginx/sites-available/default
```

- Example:

```nginx
server {
    listen 443 ssl;
    ssl_certificate     /etc/ssl/certs/pr_apache2_x509_cert.crt;
    ssl_certificate_key /etc/ssl/private/pr_apache2_pk.key;
}
```
- Test & reload:

```bash
nginx -t
systemctl reload nginx
```

---

#### Verify Certificates (Important Commands)

```bash
openssl x509 -in server.crt -noout -text
# view certificate details
```

```bash
openssl s_client -connect localhost:443
# verify live SSL service
```

---

#### Permissions & Security (Exam Critical)

```bash
ls -l /etc/ssl/private/server.key
-rw------- root root server.key
```

✔ Only **root** should read private key
❌ World-readable key = FAIL

---

####  Self-Signed Certificate Behavior (Exam Concept)

* Browser warning = expected
* Encryption = still valid
* Identity = not trusted

#### Typical LFCS Exam Tasks

✔ Generate private key
✔ Create self-signed certificate
✔ Configure HTTPS
✔ Restart service
✔ Verify using curl / ss

#### Common LFCS Mistakes (Avoid These)

❌ Forgetting to enable SSL module
❌ Wrong certificate path
❌ Incorrect file permissions
❌ Not restarting service
❌ Using HTTP instead of HTTPS

#### Quick Practice Tasks (Highly Recommended)

1️⃣ Create a self-signed cert for `localhost`
2️⃣ Enable HTTPS on Apache
3️⃣ Verify with `curl -k`
4️⃣ Check port 443
5️⃣ Inspect cert with OpenSSL

#### LFCS EXAM GOLDEN TIP

> If the question says **“secure the web server using SSL/TLS”**
> → **Self-signed cert + HTTPS is enough**


## How to enable https in local apache2 server?
1. Apache requires the mod_ssl module to handle encrypted connections.
```bash 
# Enable SSL: 
sudo a2enmod ssl
# Restart Apache: 
sudo systemctl restart apache2
```
2. Generate a Self-Signed Certificate
```bash
# Note: When prompted for "Common Name," enter localhost or your local IP address.
sudo openssl req -x509 -noenc -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt
``` 
3. Configure the SSL Virtual Host
- update SSL configuration file to use your new certificate. 
```bash
# Open the config file: 
sudo vi /etc/apache2/sites-available/default-ssl.conf
# Update certificate paths:
# SSLCertificateFile  /etc/ssl/certs/apache-selfsigned.crt
# SSLCertificateKeyFile  /etc/ssl/private/apache-selfsigned.key 
# SSLEngine on 

# Enable the site: 
sudo a2ensite default-ssl 

# Adjust Firewall (If Enabled)
sudo ufw allow "Apache Full"

# Verify and Restart
sudo apache2ctl configtest
sudo systemctl restart apache2
# confirm HTTPS port
ss -tuln | grep 443       
curl -k https://localhost
```

#### Summary Checklist

- Enable SSL Module: 	`sudo a2enmod ssl`
- Generate Cert:	`sudo openssl req ... -keyout ... -out ...`
- Enable SSL Site:	`sudo a2ensite default-ssl`
- Test Config:	`sudo apache2ctl configtest`
- Reload Apache	`sudo systemctl restart apache2`
- You should now be able to access your server at `https://localhost`. 
---


# 10. Git Operations

## create a new repository on the command line
```bash

echo "# test1" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.ibm.com/Prasad-Guntakinda/test1.git
git push -u origin main
```

## git config

### 1. The Three Configuration Scopes
- Git stores settings in different files depending on the scope. You must know how to target these specifically:

- __System (--system):__ Applied to all users on the server. (File: /etc/gitconfig).
- __Global (--global):__ Applied to your specific user. (File: `~/.gitconfig`). This is the most common for the exam.
- __Local (--local):__ Applied only to the current repository. (File: `.git/config`).

### 2. Essential Identity Configs
- You must set your identity before you can commit.
- Set Name: `git config --global user.name "Your Name"`
- Set Email: `git config --global user.email "you@example.com"`

### 3. Critical Operational Configs
- The exam may require you to optimize how Git behaves:
- Default Editor: If you are more comfortable with nano than vim, change the editor Git uses for commit messages: `git config --global core.editor "nano"`
- Default Branch Name: Ensure new repos use main instead of master:`git config --global init.defaultBranch main`
- Credential Helper: To avoid typing passwords repeatedly (if using HTTPS):
`git config --global credential.helper cache `(Stores in memory for 15 mins)
- Line Endings (LF vs CRLF): Important for cross-platform compatibility:
`git config --global core.autocrlf input` (Recommended for Linux/LFCS).

### 4. Verification Commands
- Use these to verify your work during the exam:
- List all active configs: `git config --list`
- Show where configs are coming from: `git config --list --show-origin`
- Get a specific value: `git config user.email`

### 5. Config Editing Tips
- if you want to edit the global file, first change default editor to "vim"

- To use Nano: `git config --global core.editor "nano"`
- To use Vim: `git config --global core.editor "vim"`
- To use VS Code: `git config --global core.editor "code --wait"`

- If the exam asks you to "manually edit the global configuration," use the shortcut: `git config --global --edit` (This opens your `~/.gitconfig` in the default editor).


## Summary Cheetsheet
```bash
# Setup
git init                          # start new repo
git clone URL                     # clone existing repo

# Status & history
git status                        # see status
git log --oneline                 # compact history
git diff                          # see changes

# Staging & committing
git add <file>                    # stage file
git add .                         # stage all
git commit -m "message"           # commit

# Branching
git branch                        # list branches
git checkout -b new-branch        # create + switch
git checkout branch-name          # switch branch
git merge branch-name             # merge into current branch

# Remotes
git remote -v                     # show remotes
git push -u origin branch         # push first time
git push                          # push later
git pull                          # fetch + merge

# Undo
git reset HEAD <file>             # unstage
git checkout -- <file>            # discard local changes
git revert <commit>               # safely undo commit

# Stash
git stash                         # stash changes
git stash list                    # list stashes
git stash pop                     # apply + remove latest stash
```

---



