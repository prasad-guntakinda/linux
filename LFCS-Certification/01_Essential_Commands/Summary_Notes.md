# Essential Commands Summary:


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


# 2. Hard Links and Soft Links: ln, readlink


---

# 3. Searching for Files: find

---


