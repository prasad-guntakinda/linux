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
| `adduser`             | Adds a new user.                        | `adduser john` |
| `useradd ` | Adds a new user.                        | `useradd john` |
| `passwd `  | Changes the password for a user.        | `passwd john`  |
| `chage`  | Changes user password expiry information       | `chage --maxdays 30 john`  |
| `usermod `  | Modifies the exising user account       | `usermod --lock john`  |
| `userdel ` | Deletes a user.                         | `userdel john` |



---

## PRACTICE

### useradd:

```shell
sudo useradd john
id john
#uid=1001(john) gid=1001(john) groups=1001(john)
sudo cat /etc/passwd
#john:x:1001:1001::/home/john:/bin/bash

useradd -h
#set custom UId
useradd -u 9880 john2
id john2
#both Uid and GroupId will be set to same uid
#uid=9880(john2) gid=9880(john2) groups=9880(john2)

# set custom home dir deafult: /home/username
useradd -d /test/users/j3 john3
#verify on /etc/passwd file
cat /etc/passwd

#set custom shell default: /bin/bash
useradd -s /user/bin/sh john4
#verify on /etc/passwd file
cat /etc/passwd

# all together
useradd -u 1234 -g developers -d /test/users/john5 -s /user/bin/zsh john5
#verify on /etc/passwd file
cat /etc/passwd

# how to create system accounts?

useradd --system --no-crete-home my_sys_acct


```

---

### passwd:

```shell
#default status is locked
passwd --status john
john LK 2025-10-15 0 99999 7 -1 (Password locked.)

# set password for current user
passwd
# set password for the user john
password john
#enter password
#check the status
passwd --status john
john PS 2025-10-15 0 99999 7 -1 (Password set, SHA512 crypt.)

# expires passwd immediately use has to set password on next login
passwd --expire john

# max age days, warn before expiry, inactive days before disabling account
passwd -x 30 -w 2 -i 10 john


```

---

### chage:

```shell

chage --list john
```

      [root@pr-lfcs1 ~]# chage -l john
      Last password change					: Oct 17, 2025
      Password expires					: Oct 27, 2025
      Password inactive					: Nov 06, 2025
      Account expires						: Jan 11, 1970
      Minimum number of days between password change		: 1
      Maximum number of days between password change		: 10
      Number of days of warning before password expires	: 2


```shell
# Last password change 
chage -d 2025-10-15 john
# Password expires: last password change date + max days
# Set the number of days of inactivity after a password has expired before the account is locked.
chage -I 10 john
# After expiring the password if user don't change password within 10 days then account will be locked. 
# remove account's inactivity
chage -I -1 john

#how to expire password immediately.  
chage --maxdays -1 john
# after 180 days password will expire from last password change date
chage -M 180 john

# how to warn user before 5 days of password expiry?
chage -W 5 john

```

---

### usermod: 

- By using `usermod` command we can change UID, Gid, Add to other groups, move home directory, change default shell,lock, unlock, set expiredate

```shell
usermod --home /new/home/dir --move-home john
usermod --shell /new/shell john
usermod --gid developers john
usermod --expiredate 2028-1-31 john
# set never expires
usermod --expiredate "" john
# changes user login name from john -> jane. it won't change home and mail spool
usermod --login jane john

```

### userdel:  delete a user account and related files

```shell
# deletes the user
userdel john
# Files in the user's home directory will be removed along with the home directory itself and the user's mail spool
userdel --remove john
```



## EXPERIMENT



---


## FAQs

### useradd:

<details>
<summary>What happens when a new user is created?</summary>


When a new user is created, by default system takes following actions:

- Assigns __UID__ to the user.
- Creates a home directory __/home/username__.
- Sets the __default shell__ of the user to be __/bin/sh__.
- Creates a __private user group__, named after the __username itself__.
- Contents of __/etc/skel__ are copied to the home directory of the new user.
- __.bashrc, .bash_profile and .bash_logout__ are copied to the home directory of new user.These files provide environment variables for this user’s session.
- For Regular User UID and GroupID is `>1000` and for System User UID & GroupID will be `<1000`

</details>

---

<details>
<summary> How to create a system account? </summary>

```bash
# creates a new system account, UID will be < 1000
sudo useradd --system --no-create-home mytest_sys_acct
sudo cat /etc/passwd
```
</details>

---


### passwd:
1.How to set password for user?
2.How to get password status?

### chage:
1. how to password expiry info for a user?
2. how to force user to change his password on the next log on.?
- Either by changing `chage -d 0 john` or set expiry date with older date `chage -E 2022-1-31 john`
- Use `passwd -e john`
3. How to make password never expires for an user? or how to remove password expiration for the user?
- `chage -M -1 john`
4. how to warn user before 5 days of password expiry?
- `chage -W 5 john`

<details>
<summary> How to modify existing user? </summary>

```bash


```

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
```shell

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
| `gpasswd --add <user> <gruop>`| Administer /etc/group /etc/gshadow| `gpasswd -a john dev` |
| `usermod -aG <group> <user>`  | Adds a user to a group.            | `usermod -aG developers john` |
| `groupmod ` | modify a group definition on the system | ``
| `groupdel <groupname>` | Deletes a group.                   | `groupdel developers`         |



### Pracrtice:

#### groupadd:

```shell
groupadd dev1
groupadd -g 1209 dev2
groupadd --users john2, john3 dev3
groupadd --system prod1
# verify groups info
tail /etc/group
groups john2
id john3
```

#### gpasswd & usermode: Add/Remove users to the groups

__gpasswd:__

```shell
# add one user to group
gpasswd -a john2 dev1
# add multiple users to the group
gpasswd --members john3,john4,john5 dev3
# delete user from the group
gpasswd -d john5 dev3
# verify user groups
groups john5
id -Gn john5
tail /etc/group

```

__usermod:__

```shell
# it changes the primary group
usermod -g dev2 john5
# it replaces the old secondary gruops with new list
usermod -G dev1,dev3 john5
#it appends the new group to the old list
usermod -aG prod1 john5

```


#### groupmod: modify group info

```shell
# change group id
groupadd dev2
groupmod -g 1120 dev2
groupmod --new-name programmers2 dev2
# replaces the secondary groups with new list
groupmod --users john3,john4 dev1
# append new user john2 to the list
groupmod --append --users john2 dev1
```

#### groupdel: deleting the group

```shell
# you can delete a group which is not set as a primary group for any users
groupdel dev3
# groupdel: cannot remove the primary group of user 'john5'
groupdel dev2
# you can change primary group of the user by using usermod -g or use groupdel -f to force delete group
groupdel -f dev2
```



