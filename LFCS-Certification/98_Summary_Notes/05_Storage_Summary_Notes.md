# 05_Storage_Summary_Notes

        1. Create, manage, and troubleshoot filesystems
        2. Configure and manage swap space 
        3. Configure filesystem automounters
        4. Use remote filesystems and network block devices
        5. Manage and configure the virtual file system
        6. Configure and manage LVM storage
        7. Monitor storage performance

# 1. Create, manage, and troubleshoot filesystems

- you must be able to move a raw disk from initialization to a mounted, persistent filesystem.

## 1.1 Identify and Partition Disks 

- Before creating a filesystem, you must prepare the block device. Linux stores and retrieves data from block devices

```bash
# List Block Devices: 
lsblk # (shows tree view) or 
fdisk -l.
```
- Result:
```text
root@lfcs1:~# lsblk
NAME          MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
vda           253:0    0  250G  0 disk
├─vda1        253:1    0    1M  0 part
└─vda2        253:2    0  250G  0 part
  └─vg0-lv--0 252:0    0  250G  0 lvm  /

```
- In the above response look at `TYPE` column 
        - `vda`:  is a disk and 
        - `vda1`, `vda2` both are partitions.
        - `vg0-lv--0` is LVM
- Currently we are using Virtual Machine that's why disk and partitions started with `v`, on actual server starts with `s` mean:
        - `sda`:  is a disk and 
        - `sda1`, `sda2` both are partitions.
        - `sg0-lv--0` is LVM
### understanding Naming Pattern:
```text
vda
 |-vda1
 |-vda2
vdb
 |-vdb1
 |-vdb2
```
- `vd` means `virtual disk`, `a,b,c,...` number of devices
- `vda` mean virtual disk a. `vda1,2,3,4...` number of partitions
- For Example: `vdb3` mean virtual disk b partition-3

- All these patitions are referenced by files in the `dev` directory

### Partitioning: fdisk,cfdisk

- fdisk: is used to manipulate the disk partition tables. dialog-based
- `cfdisk` 
        - is used to manipulate the disk partition tbales. cursor-based
        - It lets you create, delete, and modify partitions on a block device
```bash
# list all the partitions for vda
sudo fdisk --list /dev/vda
sudo cfdisk /dev/vda
# it will open new partition window to create, modify and delete partitions. it's self explanatory tool you can easily create partitions
```
- Disk is nothing but continuous slots of `0`s and `1`s, the job of partition table is to maintain where the partition starts and where it ends
- Two types of Partion Tables:
1. MBR (Master Boot Record): Old,  for MBR format use `dos` option
2. GPT (Grid Partition Table) : New, Always pick `gpt`


---
---

# 2. Configure and manage swap space

- Swap is used when physical RAM is full. 
- For example: if we have only 2GB RAM in that 1GB used by Video Editor and another 1GB used by audio editor. But now we want to open a chrome browser system will check which app is not using currently that app will be moved to swap area and opens a chrome browser.

### How to create file based Swap Area?
- Instead of partitions, we can also use simple files as swap. 
- In fact, if you install Ubuntu on a personal computer, that's what it will do by default nowadays. 

- To do this manually, first, we'd need to create an empty file and fill it with zeroes. That is, binary zeroes, because in binary we can have either a 0 or a 1. We'll use a utility called `dd` it will convert and copy a file

```bash
# create 128 MB swap file 
sudo dd if=/dev/zero of=/my_swap bs=1M count=128
# create 2GB swap file 
sudo dd if=/dev/zero of=/my_swap bs=1M count=2048 status=progress

```
- __Breakdown:__
        
- `if=/dev/zero` :  input file to read the data
- `/dev/zero`. It's a special device file that generates an infinite number of zeroes when an application reads from it.
- `of=/my_swap` :  output file to write the copied data 
- `bs=1M`:  specify the block size to be 1MB:
- `count=128` : combined with `bs` and `count` tells dd to write a 1 Megabyte block 128 times, so 128 megabytes in total
- `status=progress`: it display the progress it's making while writing to that file.

- Continue:
```bash
# set permissions to only allow the root user to read and write to this file:
sudo chmod 600 /my_swap
sudo mkswap /my_swap
sudo swapon --verbose /my_swap
# verify
sudo swapon --show
```
- We can use multiple files/partitions as a swap.

### How to create Non-Persistent Swap Partition? (Won't sustain after reboot)

- __Create a partition:__ by using `sudo cfdisk <dev_id>` by selecting type`Linux Swap`
- __Mark created swap partition:__ `sudo mkswap /dev/<partition_id>`
- __Activate Swap:__ `sudo swapon /dev/sdb2`
- __Check Status:__ `swapon --show` or `free -h`
- __Deactivate Swap:__ `sudo swapoff /dev/sdb2`

### How to create Persistent Swap Partition?

- Persistence: Add to `/etc/fstab`:
UUID=... none swap sw 0 0 



---

# 3. Manage and configure the virtual file system

- To store files and directories on the partitions, first we have to setup file system on it
- By default RHEL uses `xfs` file system and Ubuntu uses `ext4` file system
- To setup the filesystem on a parition we use `mkfs.<type>` command, examples `mkfs.xfs` and `mkfs.ext4`

```bash
man mkfs.xfs
# list current file systems
sudo fdisk -l
# creat xfs file system on partition
mkfs.xfs /dev/<partition>
mkfs.xfs /dev/vdb2
mkfs.ext4 /dev/vdb3
# list current properties on current ext4 file system
sudo tune2fs -l /dev/vdb3
# to see the file system types 
sudo cfdisk /dev/vda
```

## Virtual File Systems:

- Understanding the Virtual File System (VFS) involves managing pseudo-filesystems that exist in memory rather than on disk. These provide a window into the kernel, hardware, and running processes.

### 1. The Core Pseudo-Filesystems
There are three primary virtual filesystems you must know how to navigate and manage:

| Filesystem | Mount Point | Purpose |
| ---------- | ----------- | ------- |
| `procfs` | `/proc` | Information about running processes and kernel statistics.|
| `sysfs` | `/sys` | Information about hardware, drivers, and kernel subsystems. |
| `devtmpfs` | `/dev` | Access to hardware device nodes (managed by udev). |

### 2. Managing /proc (Process & Kernel Info) 

- The `/proc` directory contains numbered folders for every running PID and text files for system status.
- Process Specifics: /proc/[PID]/
```bash
cat /proc/1234/cmdline # See the command that started the process.
cat /proc/1234/environ # View environment variables for that process.
ls -l /proc/1234/exe # See the actual path to the executable.
```
- System Specifics:
```bash
cat /proc/meminfo # Detailed RAM usage (more granular than free).
cat /proc/cpuinfo # Hardware details of the CPU.
cat /proc/uptime # How long the system has been running.
cat /proc/partitions # List of partitions recognized by the kernel.
```

### 3. Managing /sys (Hardware & Runtime)
- While `/proc` is for processes, `/sys` is for hardware tree and kernel module parameters.
```bash

cat /sys/class/power_supply/BAT0/capacity # Battery/Power
cat /sys/class/net/eth0/statistics/rx_bytes # Network Stats
cat /sys/block/sda/queue/scheduler # Disk Schedulers: (allows you to change I/O schedulers on the fly).
```

### 4. Modifying Kernel Parameters (/proc/sys)
- This is a critical exam topic. The `/proc/sys` directory is the only part of the VFS that is commonly writable.
- Temporary Change (Non-persistent): `echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward`
- Persistent Change:
        - Use the `sysctl` tool to manage these settings permanently in `/etc/sysctl.conf`.
        - Translate Path to Parameter: `/proc/sys/net/ipv4/ip_forward` becomes `net.ipv4.ip_forward`.
- Apply: `sudo sysctl -p`

### 5. Mounting Virtual Filesystems
- In a recovery or `chroot` scenario (e.g., fixing a broken bootloader), you must manually mount these filesystems:
```bash
sudo mount -t proc proc /mnt/proc
sudo mount -t sysfs sys /mnt/sys
sudo mount --bind /dev /mnt/dev
```

### 6. Tools for Analysis

| Tool | What it analyzes |
| ---- | ---------------- |
| `lsblk` | Reads `/sys/dev/block` to show disk hierarchy. |
| `lscpu` | Parses `/proc/cpuinfo` | 
| `free` | Parses `/proc/meminfo` |
| `sysctl -a` | Lists all variables in `/proc/sys` | 

### LFCS Exam Checklist (2025)
- Runtime vs. Persistent: Know that editing files in `/proc` directly is not persistent.
- The `sysctl` relationship: Understand that `sysctl` is just a "pretty" interface for the files inside `/proc/sys`.
- Process Debugging: If a process is "stuck," know how to look at `/proc/[PID]/status` to see if it is in a "Zombie" (Z) or "Uninterruptible Sleep" (D) state.
- Safety: Never use `vi` or `nano` to edit files in `/proc` or `/sys`; always use `echo`, `tee`, or `sysctl`.


---
---

# 4. Configure Mount filesystem automounters

- this section will discuss on how to mount filesystems, both manually, but also automatically, at boot time.

- To make a filesystem accessible we must first mount it. 
- Mounting basically means attaching or plugging in a filesystem to one of our directories. 
- A filesystem must be attached to a directory (mount point) to be used. 
- First, we can take a look at a directory often used for temporarily mounting a random filesystem:

## Temporary Mount Non-peristent
```bash
ls /mnt/
# mount a file system /mnt directory
sudo mount /dev/vdb1 /mnt/
# create a file
sudo touch /mnt/testfile
# And we can see that our previously empty filesystem now has one file inside:
ls -l /mnt/
# check which partition is added to this dir
df -h
findmnt -T .
# view all block device
lsblk
# To unmount, we just specify the directory where our filesystem is mounted:
sudo umount /mnt/
# once you un mount it remove all the files in the file system
```
## Persistence Mounting : fstab

- __Persistent Mount:__ You must edit `/etc/fstab`
- `/etc/fstab` is the file where we can define what should be mounted automatically when the system boots up.
- A line in fstab has 6 fields. 
```ini
<file system> <mount point>   <type>  <options>      <dump>  <pass>
/dev/sdb2       /mybackups      xfs     ro,noexec        0       2
/swap.img	none	        swap	defaults	0	0

# uids are generated for each disks and it will be configured by using uids instead of partitions
# to see which partition uid belongs ls /dev/disk/by-id/
# this is useful when SSDs attachment order changes because uids are different
# Get the UUID (Universal Unique ID): blkid /dev/sdb1
/ was on /dev/vg0/lv-0 during curtin installation
/dev/disk/by-id/dm-uuid-LVM-RTQQm2CU89JW2vz3PUnV92Jp2X6EKdD23Nk7NJa4oYuZcGsqPU5YlWmU5FPDVC8g / xfs defaults 0 1

```

- `<file system>`: existing file system `/dev/sdb2`, or `/dev/vda1`
- `<mount point>`: the mount point (target) for the filesystem like `/` at root or `/mnt` or `/mybackups`, for swap it is `none`
- `<type>`: type of the filesystem. Linux supports many filesystem types: `xfs`, `ext4`, `ntfs`, `swap` and so on.
- `<options>`: mount options associated with the filesystem: `ro`, `rw`, `suid`, `dev`, `exec`, `auto`, `nouser`, and `async`. Most scenarios use `defaults` as an option
- `<dump>`: determine which filesystems need to be dumped, default is `0`
- `<pass>`: Scan order in which filesystem checks are done at boot time. 
        - `0` Defaults to zero (don’t check the filesystem)
        - `1`: The root filesystem (where os is installed) should be specified with a value of 1. 
        - `2`: Other filesystems should have a value of 2. 

- 

- Test and Reload Config or reboot the system
```bash
# Test fstab: 
sudo mount -a # If this command throws an error, fix it immediately or the system won't boot. 
systemctl daemon-reload # it reloads all the configuration files including /etc/fstab
# or you can reboot 
systemctl reboot
```
### Adding Mount using UUID:

-  Get the UUID (Universal Unique ID): `blkid /dev/sdb1`
- Add to `/etc/fstab`:
`UUID=550e8400-e29b... /mnt/data ext4 defaults 0 2`
- Test fstab: `sudo mount -a` Critical: If this command throws an error, fix it immediately or the system won't boot. 


## File system and Mount Options

```bash
# list all the mounts 
findmnt
# show only xfs and ext4 mounts
findmnt -t xfs,ext4
# create a mount with read-only filesystem
sudo mount -o ro /dev/sdb2 /mnt
# verify options
findmnt
touch testfile /mnt/testfile # fails because of read-only option
# multiple options
sudo mount -o rw,noexec,nosuid /dev/vdb2 /mnt
sudo mount -o remount,rw,noexec,nosuid /dev/vdb2 /mnt
```


<details>
<summary> auto mounters:</summary>

For the LFCS (Linux Foundation Certified System Administrator) exam in 2025, you must know how to configure the autofs utility. Unlike static mounts in /etc/fstab, an automounter mounts a filesystem only when it is accessed and unmounts it after a period of inactivity.
1. Benefits of Automounting
Performance: Reduces system boot time by not mounting everything at once.
Efficiency: Conserves system resources and network bandwidth (crucial for NFS).
Stability: Prevents boot failures if a remote server (NFS/Samba) is unavailable at startup.
2. Installation and Service
On Ubuntu 24.04/25.x, install the autofs package:
Install: sudo apt update && sudo apt install autofs -y
Service Management:
sudo systemctl enable --now autofs
sudo systemctl status autofs
3. Configuration Files Hierarchy
Autofs uses a "Master Map" which then points to specific "Map Files."
Master Map (/etc/auto.master): Defines the base mount point and which map file to read.
Map Files (/etc/auto.<name>): Defines the specific subdirectories, mount options, and source devices.
4. Configuration Steps (Example: Mounting NFS)
Task: Automount an NFS share from 192.168.1.10:/data onto /mnt/remote/data whenever a user enters that directory.
Step 1: Edit the Master Map
Add a line to /etc/auto.master:
bash
/mnt/remote  /etc/auto.nfs  --timeout=60
Use code with caution.

/mnt/remote: The parent directory (autofs will manage this).
/etc/auto.nfs: The file containing the specific rules.
--timeout=60: Unmount automatically after 60 seconds of inactivity.
Step 2: Create the Map File
Create and edit /etc/auto.nfs:
bash
data  -fstype=nfs,rw,soft,intr  192.168.1.10:/data
Use code with caution.

data: The name of the subdirectory that will be created inside /mnt/remote.
-fstype=nfs,rw...: The mount options.
192.168.1.10:/data: The source location.
Step 3: Apply and Test
Reload Autofs: sudo systemctl restart autofs
Verify: The directory /mnt/remote/data will not exist yet or will appear empty.
Trigger the mount: cd /mnt/remote/data or ls /mnt/remote/data.
Confirm: Run df -h or mount | grep nfs to see the active mount.
5. Advanced: Wildcard Maps
If you have multiple home directories or shares on one server, use wildcards to avoid listing every single one.
Map file entry:
* -fstype=nfs,rw,soft 192.168.1.10:/home/&
If you access /mnt/remote/alice, autofs will automatically mount 192.168.1.10:/home/alice.
6. Troubleshooting
Tool	Action
Check Logs	journalctl -u autofs -f
Test Syntax	automount -v -f (Runs in foreground to show errors)
Check Maps	Ensure map files are not executable (unless they are scripts).
LFCS Exam Checklist (2025)
Dependency: Ensure the client tools for the filesystem are installed (e.g., nfs-common for NFS).
Directory Creation: You do not need to manually create the subdirectories (like data in the example); autofs creates them dynamically.
Timeout: Always set a timeout if requested to ensure the "automatic unmount" functionality works.
Permissions: Ensure the map files are readable by root.
</details>
---
---

# 5. Use remote filesystems and network block devices: NFS, NBD

## 5.1 Network File System Protocol: NFS

- There are multiple protocols that allow us to access remote filesystems.
- A protocol is, in a way, a "language" that a client and server use to communicate. They need to "speak" the same language so that they can understand each other, negotiate a connection, and transfer data.

- NFS allows a server to share directories over the network. Clients mount these as if they were local folders.

### Server Configuration (The Exporter)

```bash
# Install: 
sudo apt install nfs-kernel-server
# Create Share: 
sudo mkdir -p /mnt/nfs_share && sudo chown nobody:nogroup /mnt/nfs_share
```

- Configure Exports: Edit `/etc/exports`:
` /mnt/nfs_share 192.168.1.0/24(rw,sync,no_subtree_check)`
- Options: `rw` (read-write), `ro` (read-only), `sync` (write changes immediately).
```ini
/etc *.example.com(ro, sync, no_subtree_check) # shares /etc to all sub domains of example.com
/etc *(ro, sync, no_subtree_check) # accessed by any client
```
- Apply: 

```bash
sudo exportfs -ra 
sudo systemctl restart nfs-kernel-server
# see current exports 
sudo exportfs -v
```

### Client Configuration (The Mounter)
- Install: `sudo apt install nfs-common`
- Temporary Mount:
```bash
sudo mount IP_or_hostname_of_server:/path/to/remote/directory /path/to/local/directory
sudo mount server1:/etc /mnt
sudo mount 192.168.1.10:/mnt/nfs_share /mnt/local_mount

```
- Persistent Mount (`/etc/fstab`):
```ini
server1:/etc /mnt nfs defaults 0 0
192.168.1.10:/mnt/nfs_share /mnt/local_mount nfs defaults,_netdev 0 0

```
- Note: `_netdev` ensures the system waits for the network to be up before trying to mount.


---
## 5.2. Network Block Devices: NBD

### What is NBD?

- Unlike NFS (which shares files), NBD shares a block device (like a hard drive, partition, or a .qcow2 file) over the network. 
- The client machine sees it as a local disk (e.g., /dev/nbd0), which it can partition, format, and mount.

###  Setup NBD Server (The Provider)
- The server exports a file or device to the network.
- Install: `sudo apt update && sudo apt install nbd-server -y`
- Configure the Export: Edit `/etc/nbd-server/config`
```ini
[generic]
    # Core settings for the server
    # comment user=nbd and group=nbd lines
    allowlist=true
[partition2]
    exportname = /dev/sdb2
```
- To see all the possible option use: `man 5 nbd-server` open man for `/etc/nbd-server/config`
- Restart Service: `sudo systemctl restart nbd-server`
### 3. Setup NBD Client (The Consumer):

- The client connects to the server and maps the remote storage to a local device node.
- Install: `sudo apt install nbd-client -y`
- Load Kernel mod: `sudo modprobe nbd` but we need to reload this module after every reboot, 
- For persistent config modify `sudo vim /etc/modules-load.d/modules.conf` add `nbd` and save
- Connect to the Server: 
```bash
sudo nbd-client -l <server_ip> # list all the exported names
sudo nbd-client <Server_IP> -N <name_of_the_export>
sudo nbd-client 10.127.12.13 -N partition2
# Verify the Device:
lsblk  # should now show /dev/nbd0 as a disk
```
- `/dev/nbd0` added as a local block device but it is actually server's `/dev/sdb2` which we mentioned on `/etc/nbd-server/config`
- Format and Mount (First time only):
```bash
sudo mkfs.ext4 /dev/nbd0
sudo mkdir /mnt/nbd
sudo mount /dev/nbd0 /mnt/nbd
```

#### Disconnecting Client
- When finished, unmount the filesystem and then disconnect the block device.
```bash
sudo umount /mnt/nbd
sudo nbd-client -d /dev/nbd0
```

## Key Comparisons for LFCS Exam

| Feature | NFS | NBD / iSCSI |
| ------- | --- | ----------- | 
| Data Unit | Files/Folders | Raw Blocks |
| Client Role | Accesses existing files. | Must create own filesystem (mkfs). |
| Simultaneous Access | Safe for many clients. | Dangerous! Multiple clients writing to one NBD without a cluster-aware FS will corrupt data. |
| Port | 2049 | 10809 (Default for NBD) |


## Troubleshooting Checklist
- Firewall:
        - NFS: Allow ports 2049 (NTP) and 111 (RPC).
        - iSCSI: Allow port 3260.
- Persistence: To connect NBD at boot, edit /etc/nbd-client:

```text
# /etc/nbd-client
KILLALL="true"
NBD_DEVICE[0]=/dev/nbd0
NBD_HOST[0]=192.168.1.10
NBD_PORT[0]=10809
```
- FSTAB: If mounting NBD via `/etc/fstab`, always use the `_netdev` option to prevent the system from hanging if the network is slow during boot.
- Permissions: If NFS is "Read Only" unexpectedly, check both `/etc/exports` on the server and the Linux permissions of the directory.
- Mount Failures: Always check `journalctl -xe` and verify connectivity with `ping` or `nc -zv <IP> <PORT>`.
LFCS Exam Pro-Tip:
- If the task asks to "Share a directory for multiple users," use NFS. 
- If the task asks to "Attach a 50GB disk to the server over the network," or "share a disk image so another machine can use it as a local block device," use NBD.

---
---

# 6. Configure and manage LVM storage

- Logical Volume Management (LVM) is a high-priority topic. 
- LVM provides a layer of abstraction over physical storage, allowing you to resize partitions and span filesystems across multiple disks without reformatting. 

## The LVM Hierarchy
- To build LVM storage, you must follow this specific order:
        1. Physical Volumes (PV): Raw disks or partitions (e.g., /dev/sdb, /dev/sdc1).
        2. Volume Groups (VG): A pool of storage created by combining one or more PVs.
        3. Logical Volumes (LV): The "virtual partitions" created from the VG. You format and mount these. 
        4. 
- use `sudo lvmdiskscan` to list what PVs we can use

## Creating LVM Storage (Step-by-Step)

- Scenario: Create a 5GB volume named `lv_data` inside a group named `vg_sales` using a new disk `/dev/sdb`.

- Step 1: Initialize the Physical Volume: 
```bash
sudo pvcreate /dev/sdb 
# list all PVs
sudo pvs
```
- Step 2: Create the Volume Group `sudo vgcreate vg_sales /dev/sdb`
- Step 3: Create the Logical Volume `sudo lvcreate -L 5G -n lv_data vg_sales`
        - `-L`: Specifies size (e.g., 5G, 500M).
        - `-n`: Specifies the name of the LV. 
- Step 4: Format and Mount 
```bash
# logical volumes location: /dev/name_of_the_VG/name_of_the_LV
sudo mkfs.ext4 /dev/vg_sales/lv_data
sudo mount /dev/vg_sales/lv_data /mnt/sales 
```

##  Managing and Resizing (Critical for Exam)
- The primary benefit of LVM is the ability to grow volumes while the system is running. 
-  Extending an LV (Growing)
- Task: Increase `lv_data` to 10GB. 
- Extend the LV: `sudo lvextend -L 10G /dev/vg_sales/lv_data`
- Extend 100% remaing VG: `sudo lvresize --extends 100%VG /dev/vg_sales/lv_data`
- If file system is already setup then resize filesystem as well: `sudo lvresize  --resizefs --extends 100%VG /dev/vg_sales/lv_data`

## Resize the Filesystem:
- For Ext4: `sudo resize2fs /dev/vg_sales/lv_data`
- For XFS: `sudo xfs_growfs /mnt/sales` (XFS uses the mount point). 
- Shortcut: Use `lvextend -r -L +2G ...` to extend and resize in one command. 

## Adding Space to a Volume Group 
- If your VG is out of space, add a new physical disk: 
```bash
sudo pvcreate /dev/sdc
sudo vgextend vg_sales /dev/sdc 
```

## LVM Snapshots 
- Snapshots are "frozen" copies of an LV used for backups. 
- Create: `sudo lvcreate -L 1G -s -n lv_snap_data /dev/vg_sales/lv_data`
- Restore: `sudo lvconvert --merge /dev/vg_sales/lv_snap_data`

## Essential Commands for Information

| Tool | Purpose |
| `pvs / pvdisplay` | View physical volumes.|
| `vgs / vgdisplay` | View volume groups (check "VFree" for available space). |
| `lvs / lvdisplay` | View logical volumes. |
| `lsblk` | Shows the visual relationship between disks and LVM. |


## Troubleshooting & Persistence

- Naming: LVs are accessible via `/dev/vg_name/lv_name` or `/dev/mapper/vg_name-lv_name`.
- FSTAB: Use the LVM path in `/etc/fstab` for persistence:
`/dev/vg_sales/lv_data /mnt/sales ext4 defaults 0 2`

- Removal Order: To delete LVM, go in reverse: `umount -> lvremove -> vgremove -> pvremove`. 

## LFCS Exam Checklist (2025)
- Shrinking: Remember that XFS cannot be shrunk. Ext4 can, but it must be unmounted first.
- PE Size: If the exam asks to create a VG with a specific Physical Extent (PE) size, use vgcreate -s 16M ....
- Free Space: If asked to use all remaining space in a VG for an LV, use lvcreate -l 100%FREE ....
- UUIDs: While LVM paths are stable, blkid can still be used to find the UUID of an LV for /etc/fstab. 

---
---

# 7. Monitor storage performance

- Just like CPU and RAM, storage devices have their limits. If a process is overusing the CPU, keeping it at 100%, the system will slow down considerably. And the same can happen if a storage device is overused.
- We have utilities like "top", and "htop", to look at how processes are using the CPU and our RAM.
- There are many tools that can monitor read/write operations. There's a package called "sysstat" that contains a few such tools.
```bash
sudo apt install sysstat
```
- This contains a few other system statistic utilities, but the ones we're interested in are called "iostat" and "pidstat".

- __iostat__'s name comes from I/O statistics. And I/O is short for Input/Output. Because we can input data to a storage device, when we're writing to it. And the storage device outputs data when we read from it.
- __pidstat__ is short for "process ID statistics". Each process on Linux has an unique IDentifier. So this tool will show us statistics for each process on our system, alongside their ID numbers.

```bash
# shows hitorical data since bootup
sudo iostat
# show data from last 5 seconds and refreshes screen every 5 seconds
iostat 5 

# to see iostats on all partitions on the device
iostat -p ALL
# to see specific device partitions
iostat -p vda
```
- Output:

```text
Device             tps    kB_read/s    kB_wrtn/s    kB_dscd/s    kB_read    kB_wrtn    kB_dscd
dm-0              0.36         4.65        29.89         0.00     513364    3301070          0
loop0             0.00         0.00         0.00         0.00         24          0          0
vda               0.35         4.71        29.89         0.00     520624    3301070          0

```

- Using `pidstat`

```bash
# always use -d to show how the storage device is using
pidstat -d
```
- to show device manager setup info a device

```bash
sudo dmsetup info
# to see for a specific device
sudo dmsetup info /dev/dm-0
```


---
---

# Special Files Permissions Notes:

- ACLs

## Change Attribut: chattr

1. How to make file only appendable but not overwritten by any user?

```bash
users can only append the data cannot be overwritten
chattr +a file1
# remove attribute
chattr -a file1

# how make file immutable?
chattr +i file1 # cannot be edited/deleted even root user cannot edit
chattr -i file1
# list all attributes of a file
lsattr file1
```
