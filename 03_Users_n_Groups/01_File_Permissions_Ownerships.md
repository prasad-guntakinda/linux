#  File Permissions and Ownership

File permissions in Linux are based on users and groups. Each file has an owner, a group, and associated permissions.

### Permission Types
| Permission    | Description                                           |
| ------------- | ----------------------------------------------------- |
| `r` (read)    | Allows reading the file or directory.                 |
| `w` (write)   | Allows modifying the file or directory.               |
| `x` (execute) | Allows executing the file or accessing the directory. |

### Viewing Permissions
Use the `ls -l` command to view file permissions:
```bash
ls -l
```
Example output:
```
-rw-r--r-- 1 john developers 1024 Oct 11 12:00 file.txt
```
- `-rw-r--r--`: File permissions.
- `john`: File owner.
- `developers`: File group.

### Changing Permissions
| Command | Description               | Example                     |
| ------- | ------------------------- | --------------------------- |
| `chmod` | Changes file permissions. | `chmod 755 file.txt`        |
| `chown` | Changes file owner.       | `chown john file.txt`       |
| `chgrp` | Changes file group.       | `chgrp developers file.txt` |


