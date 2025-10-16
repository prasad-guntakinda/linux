# Linux Users and Groups

In Linux, users and groups are fundamental components for managing system security and permissions. They help control access to files, directories, and system resources.

## LEARN
### Users

- A user in Linux is an account that can log in and perform tasks.
- Each user has a unique identifier (UID) and associated permissions.
- All the users info are stored in `/etc/passwd` file. 
- Hashed passwords for users are stored in `/etc/shadow` file.

#### Types of Users
| User Type    | Description                                                                 |
| ------------ | --------------------------------------------------------------------------- |
| Root User    | The superuser with unrestricted access to the system.                       |
| Regular User | A standard user with limited permissions.                                   |
| System User  | A user created by the system for running specific services or applications. |

#### User Information on /etc/passwd

User details are stored in the `/etc/passwd` file. Each line represents a user and contains the following fields:
```
username:x:UID:GID:comment:home_directory:shell
```
- `username`: The name of the user.
- `UID`: User ID, a unique identifier for the user.
- `GID`: Group ID, the primary group of the user.
- `home_directory`: The user's home directory.
- `shell`: The default shell for the user.

### Common Commands for User Management
| Command              | Description                             | Example        |
| -------------------- | --------------------------------------- | -------------- |
| `whoami`             | Displays the current user.              | `whoami`       |
| `id`                 | Displays user ID and group information. | `id`           |
| `adduser <username>` | Adds a new user.                        | `adduser john` |
| `useradd <username>` | Adds a new user.                        | `useradd john` |
| `userdel <username>` | Deletes a user.                         | `userdel john` |
| `passwd <username>`  | Changes the password for a user.        | `passwd john`  |


---

## PRACTICE




---


## EXPERIMENT



---


## FAQs


<details>
<summary>What happens when a new user is created?</summary>

When a new user is created, by default system takes following actions:

- Assigns __UID__ to the user.
- Creates a home directory __/home/username__.
- Sets the __default shell__ of the user to be __/bin/sh__.
- Creates a __private user group__, named after the __username itself__.
- Contents of __/etc/skel__ are copied to the home directory of the new user.
- __.bashrc, .bash_profile and .bash_logout__ are copied to the home directory of new user.These files provide environment variables for this user’s session.

</details>

---

---

## Groups

A group in Linux is a collection of users. Groups are used to manage permissions for multiple users efficiently.

### Types of Groups
| Group Type      | Description                             |
| --------------- | --------------------------------------- |
| Primary Group   | The default group assigned to a user.   |
| Secondary Group | Additional groups a user can belong to. |

### Group Information
Group details are stored in the `/etc/group` file. Each line represents a group and contains the following fields:
```
group_name:x:GID:member1,member2
```
- `group_name`: The name of the group.
- `GID`: Group ID, a unique identifier for the group.
- `member1,member2`: Users who are members of the group.

### Common Commands for Group Management
| Command                | Description                        | Example                       |
| ---------------------- | ---------------------------------- | ----------------------------- |
| `groups <username>`    | Displays groups a user belongs to. | `groups john`                 |
| `groupadd <groupname>` | Adds a new group.                  | `groupadd developers`         |
| `groupdel <groupname>` | Deletes a group.                   | `groupdel developers`         |
| `usermod -aG <group>`  | Adds a user to a group.            | `usermod -aG developers john` |

## File Permissions and Ownership

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

## Examples

### Adding a New User

```shell
    #Add a new user
    sudo useradd john
    #Set a password for the user
    sudo passwd john
    #Verify the user
    id john
    #user information
    sudo cat /etc/passwd
    #delete user
    sudo userdel john
    #  -r, --remove  remove home directory and mail spool
    sudo userdel -r john

    # to create a custome home dir. default location: /home/<username>
     sudo useradd -d /test_user/john john
    # to assign different shell to user. default /bin/bash
     sudo useradd -s /bin/sh john
    # you can use both options in one command
    sudo useradd -d /test_users/john -s /bin/sh john
```

### Adding a User to a Group
1. Create a new group:
   ```bash
   groupadd team
   ```
2. Add the user to the group:
   ```bash
   usermod -aG team alice
   ```
3. Verify the user's groups:
   ```bash
   groups alice
   ```

### Changing File Ownership
1. Change the owner of a file:
   ```bash
   chown alice file.txt
   ```
2. Change the group of a file:
   ```bash
   chgrp team file.txt
   ```

## Summary
Understanding users and groups is essential for managing permissions and ensuring system security.
