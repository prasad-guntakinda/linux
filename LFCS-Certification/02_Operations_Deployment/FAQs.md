# FAQs


<details>
<summary>Does every command like <code>ls</code>, <code>cd</code>, <code>mkdir</code> etc. create a process in Linux?</summary>

<strong>Answer (short):</strong> No — not every command spawns a new process. It depends on whether the command is an external program, a shell builtin, a shell function/alias, or runs in a subshell or pipeline.

<strong>Explanation & examples:</strong>

- <strong>External commands</strong> (usually in `/bin`, `/usr/bin`, etc.) create a new process. Examples: `ls`, `mkdir`, `grep`, `python3`.
	- The shell forks and the child executes the program (`execve`). You can confirm with `type -a ls` or `command -v ls`.

- <strong>Shell builtins</strong> run inside the shell process and do not create a new process. Examples: `cd`, `exit`, `export`, `alias`, `read`.
	- Builtins are necessary when a command must change shell state (for example, `cd` must run in the current shell to change its working directory).

- <strong>Shell functions and aliases</strong> run in the shell. They only create child processes if they invoke external programs.

- <strong>Subshells</strong> (created with parentheses `( ... )`) and pipelines generally create new processes for each element. Example: `(cd /tmp; pwd)` runs in a subshell so the `cd` does not affect the parent shell.

- <strong>`exec` builtin</strong> replaces the current shell process with the program — it does not create a new process (the shell PID becomes the program's PID).

- <strong>Scripts</strong>:
	- Running `./script.sh` or `bash script.sh` starts a new interpreter process. 
	- Using `source script.sh` or `. script.sh` executes the script in the current shell (no new process).

<strong>Useful commands to check behavior:</strong>

```bash
# show whether a command is builtin or external
type -a cd       # usually shows 'cd is a shell builtin'
type -a ls       # shows path to /bin/ls (external)

# observe shell PID and a child process
echo "shell PID=$$"
sleep 20 &       # external command — creates a child
ps -o pid,ppid,cmd --forest | head -n 20
```

<strong>Demo you can run:</strong>

```bash
# 1) see your shell PID
echo "shell PID=$$"

# 2) builtin (no new PID):
type -a cd
cd /tmp
echo "still shell PID=$$"

# 3) external command (creates a child process):
type -a sleep
sleep 20 &
echo "started sleep in background; check with ps"
ps -o pid,ppid,cmd --forest | grep sleep -B2 -n || true
```

<strong>Why it matters:</strong>

- Builtins are faster (no fork/exec) and required to change the shell state.
- Knowing which commands spawn processes helps when writing scripts (avoid unnecessary forks), debugging, and understanding performance.

</details>

---



<details>
<summary>How to send currently editing vim session to background job?</summary>

- To send a running Vim editing session to the background as a job in a Unix-like environment (e.g., Linux, macOS), follow these steps:
- __Suspend Vim:__ While in Vim, press ``Ctrl+Z``. This suspends the Vim process and sends it to the background, returning you to your shell prompt.

- __Verify Background Job:__ At your shell prompt, you can type ``jobs`` to list the currently running background jobs. You should see Vim listed among them.

- __Bring Vim to Foreground (Optional):__ If you need to resume editing in Vim, type ``fg`` at your shell prompt. This brings the last background job (Vim in this case) back to the foreground. If you have multiple background jobs and want to resume a specific one, use ``fg %<job_number>``, where ``<job_number>`` is the number associated with Vim in the jobs list.


</details>

---

<details>
<summary> How to run a job directly in background? </summary>

To make a job run in the background in Linux instead of bringing it to the foreground, you can use the following methods: 

1. __Starting a command directly in the background:__ 
Append an ampersand (&) to the end of the command when you execute it. This tells the shell to run the command in the background immediately and return control of the terminal to you. 

````bash
# your_command &
sleep 400 &

````
2. __Sending a running foreground job to the background:__
    
If a command is already running in the foreground, you can send it to the background by: 

- __Suspending the job:__ Press ``Ctrl + Z``. This will pause the job and return you to the shell prompt. 

- __Resuming the job in the background:__ Type ``bg`` and press Enter. This will continue the last suspended job in the background. To run specific job ``bg <job_number>``


3. __Using nohup for processes that should continue after terminal closure:__ 
If you want a process to continue running even after you close your terminal session (e.g., when connecting via SSH), use the nohup command in conjunction with the ampersand: 
````bash
nohup your_command &
````
This command redirects the output to a file named ``nohup.out`` by default, preventing the process from terminating when the controlling terminal closes. 

#### Important Notes: 

- To see a list of jobs running in the background, use the ``jobs`` command. 
- To bring a backgrounded job back to the foreground, use the ``fg`` command. You can specify a job ID (e.g., ``fg %1``) or simply type ``fg`` to bring the most recent background job to the foreground. 
- For long-running tasks or managing multiple detached sessions, consider using tools like ``screen`` or ``tmux``. These tools allow you to create persistent terminal sessions that you can detach from and reattach to later, even from different locations or after closing your initial connection.

#### Example:

- execute ``sleep 500`` and move to background using ``ctrl+z``
- check on ``jobs`` it will be on stopped state
- If you want to run the job without bringing them to foreground use ``bg <job_number>`` now check ``jobs`` you can see Running state
- To bring foreground ``fg <job_number>``


</details>

---

<details>
<summary> What is mount & unmount in linux?</summary>

Below is the **GitHub-friendly Markdown version** of the explanation for **mount and unmount in Linux filesystems** — clean, structured, and ready for your notes or GitHub repo.

---

# **Mount and Unmount in Linux Filesystems**

In Linux, **mounting** and **unmounting** are essential operations for making storage devices usable.

---

## 🟦 What is *Mounting*?

**Mounting** means:

> Attaching a filesystem (disk/partition/USB) to a directory so that its contents become accessible.

Linux does **not** use drive letters (like C:, D:).
Instead, everything is placed inside a **single directory tree** starting at `/`.

### ✔ Example — Mount a device

```bash
mount /dev/sdb1 /mnt/data
```

Now all files inside `/dev/sdb1` become available under `/mnt/data`.

### ✔ Any directory can be used as a mount point:

* `/mnt`
* `/media`
* `/data`
* `/backup`
* `/opt/disk1`

---

## 🟥 What is *Unmounting*?

**Unmounting** means:

> Safely detaching a filesystem so the device can be removed or checked.

### ✔ Example — Unmount a device

```bash
umount /mnt/data
```

or

```bash
umount /dev/sdb1
```

### ❗ If device is busy:

```bash
umount: /mnt/data: target is busy
```

Check what is using it:

```bash
lsof /mnt/data
fuser -vm /mnt/data
```

---

## 🟨 Why Mounting/Unmounting Is Needed?

Linux requires mounting because:

* It has **one unified filesystem tree**
* Devices are **not automatically attached**
* Mounting tells Linux where the filesystem should appear

---

## 🟩 Devices That Need Mounting

| Device Type              | Examples                 |
| ------------------------ | ------------------------ |
| Hard disk partitions     | `/dev/sda1`, `/dev/sdb2` |
| USB drives               | `/dev/sdc1`              |
| LVM logical volumes      | `/dev/vg/data`           |
| ISO images               | `image.iso`              |
| Network shares (NFS/SMB) | `192.168.1.10:/share`    |

---

## 🟪 Mounting Different Filesystem Types

### Mount EXT4

```bash
mount -t ext4 /dev/sdb1 /mnt
```

### Mount XFS

```bash
mount -t xfs /dev/sdb1 /mnt
```

### Mount ISO file

```bash
mount -o loop my.iso /mnt/iso
```

### Mount NFS share

```bash
mount -t nfs 192.168.10.50:/data /mnt/nfs
```

---

## 🟫 Persistent Mounting (Auto-mount on Boot)

Add to `/etc/fstab`:

```
/dev/sdb1   /data   xfs   defaults   0 0
```

Apply:

```bash
mount -a
```

---

## 🔧 Check What Is Currently Mounted

### All mounted devices:

```bash
mount
```

### Modern way (recommended):

```bash
findmnt
```

### Disk usage + mount points:

```bash
df -h
```

---

## 🧪 Summary

### ✔ `mount`

Attach a filesystem to a directory.

### ✔ `umount`

Detach a filesystem safely.

### ✔ Commonly used for:

* disks
* partitions
* USB drives
* LVM volumes
* ISO images
* network shares

### ✔ Persistent mounting handled via:

`/etc/fstab`

---

If you'd like, I can turn this into:

📘 A **Mount & Filesystem Cheat Sheet PDF**
🧪 A **practice set for LFCS filesystem tasks**

Just tell me!

</details>

---

<details>
<summary>Difference b/w DAC vs MAC</summary>


The main difference is that Discretionary Access Control (DAC) allows resource owners to control access based on ownership, while Mandatory Access Control (MAC) enforces access based on system-wide, administrator-defined security policies and user security labels. DAC is flexible and user-friendly but less secure, whereas MAC is more rigid, highly secure, and typically used in environments with strict security needs like government and military. [1]  

| Feature [1] | Discretionary Access Control (DAC) | Mandatory Access Control (MAC)  |
| --- | --- | --- |
| Control | Decentralized; users decide who can access resources they own. | Centralized; administrators and the system define and enforce policies.  |
| Basis for access | User identity and ownership. | System-defined security labels, clearances, and policies.  |
| Security Level | Lower. | Higher.  |
| Flexibility | High; users can change permissions freely. | Low; rigid and based on strict rules.  |
| Implementation | Easier to implement and maintain. | More difficult and complex to implement.  |
| Use Case | Standard user-facing systems like file systems and social networks. | High-security systems, such as military, government, and intelligence.  |



## DAC:
In Linux, discretionary access control (DAC) means that the owner of a file or resource has the discretion to decide who can access it and what permissions they have (read, write, execute). This decentralized model is the traditional, default security model in Linux, managed using commands like chmod and chown. While flexible, it offers minimal protection against malware or misconfigurations since a process running as a user has all the same permissions as that user. [1, 2, 3, 4, 5, 6, 7]  
How it works in Linux 

• Ownership: Every file and directory has an owner (a specific user) and a group owner. 
• Permissions: The owner can set permissions for three categories of users: the user who owns the file, members of the file's group, and everyone else. 
• Levels of access: The permissions available for each category are: 

	• Read: The ability to view the file's contents. 
	• Write: The ability to modify or delete the file. 
	• Execute: The ability to run the file as a program. 

• Control mechanism: The  command is used to change these permissions, while  is used to change the owner of a file. [2, 3, 5, 7, 8]  

Example 

• A user can create a text file, and as its owner, they can grant a colleague read-only access while denying it to others. 
• The owner can also give another user write permission, allowing them to edit the file. [2]  

Limitations 

• DAC can be less secure because it relies on user discretion. If a user grants too many permissions, or if malware running with that user's privileges is executed, it can lead to a security breach. 
• It is difficult to enforce a strict, system-wide security policy with DAC alone, and it offers less granular control than other models like Mandatory Access Control (MAC). 


---

## MAC:
Mandatory Access Control (MAC) is a security model that restricts access to system resources based on predefined, system-wide security policies, not on the discretion of individual users. In a MAC system, every user and every resource has a security label, and a user's clearance level (label) determines whether they can access a resource based on its sensitivity label. This model is enforced by the operating system itself, making it more rigid and secure than Discretionary Access Control (DAC), where users can grant or revoke access to their own files. 

• System-enforced policies: The system-wide security policy, set by administrators, is mandatory for all users, unlike DAC where the owner controls permissions. 
• Security labels: Both users and resources are assigned security labels. For example, a user might have a "secret" clearance, and a file might be labeled "confidential". 
• Access rules: Access is granted only if the user's clearance level meets or exceeds the resource's sensitivity level, as defined by the policy. 
• High-security environments: MAC is typically used in high-security environments like military or government systems where strict control is paramount. 
• Examples: SELinux (Security-Enhanced Linux) is a well-known example of a MAC system, as explained by Android Open Source Project (https://source.android.com/docs/security/features/selinux/concepts) and Oracle Help Center (https://docs.oracle.com/en/operating-systems/oracle-linux/6/porting/section_jsf_zpm_wm.html).

</details>