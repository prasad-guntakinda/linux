# Storage CheetSheet:


Perfect 👍
Below is your **enhanced LFCS Storage Cheat Sheet with MEMORY-FRIENDLY COMMENTS**, designed so that **one quick scan triggers recall during the exam**.
I’ve added **inline comments (`#`)**, **mental hooks**, and **why/when notes** — without bloating it.

---

# 🧠 LFCS STORAGE – FINAL REVISION CHEAT SHEET (With Recall Comments)

---

# 1️⃣ Create, Manage & Troubleshoot Filesystems

---

## 🔹 Identify disks & filesystems

```bash
lsblk              # Shows disk → partition → mount structure
lsblk -f           # Shows filesystem + UUID (VERY IMPORTANT)
blkid              # Shows UUID & FS type (used in fstab)
df -hT             # Mounted FS usage + type
mount | column -t  # Currently mounted filesystems
```

---

## 🔹 Create Partitions

### MBR (fdisk)

```bash
fdisk /dev/sdb
# n → new
# p → primary
# 1 → partition number
# enter → default start
# enter → full disk
# w → write changes
```

### GPT (parted)

```bash
parted /dev/sdb mklabel gpt
parted /dev/sdb mkpart primary ext4 1MiB 100%
```

👉 **Use GPT for disks >2TB**

---

## 🔹 Create Filesystem

```bash
mkfs.ext4 /dev/sdb1     # most common
mkfs.xfs /dev/sdb1      # enterprise standard
mkfs.vfat /dev/sdb1     # FAT (USB)
```

### Label filesystem (good practice)

```bash
e2label /dev/sdb1 DATA01
xfs_admin -L DATA01 /dev/sdb1
```

---

## 🔹 Mount filesystem

### Temporary mount (NOT persistent)

```bash
mount /dev/sdb1 /mnt/data
umount /mnt/data
```

### Persistent mount (/etc/fstab)

```bash
UUID=xxxx  /mnt/data  ext4  defaults  0  2
```

👉 **Always test before reboot**

```bash
mount -a
```

---

## 🔹 Filesystem check (Repair)

```bash
fsck /dev/sdb1         # general
fsck.ext4 /dev/sdb1
xfs_repair /dev/sdb1   # xfs only
```

⚠️ Never run fsck on mounted FS

---

## 🔹 Resize Filesystem

### EXT4 (grow/shrink)

```bash
resize2fs /dev/sdb1
```

### XFS (ONLY grow)

```bash
xfs_growfs /mnt/data
```

---

## 🔹 Troubleshooting

```bash
dmesg | tail          # kernel errors
journalctl -xe        # service issues
lsblk -f              # missing mount?
```

---

# 2️⃣ Configure & Manage Swap Space

---

## 🔹 Check swap

```bash
swapon --show
free -h
```

---

## 🔹 Create swap file (Recommended)

```bash
fallocate -l 1G /swapfile
chmod 600 /swapfile        # MUST (security)
mkswap /swapfile
swapon /swapfile
```

### Persistent swap

```bash
/swapfile none swap sw 0 0
```

---

## 🔹 Swap partition

```bash
mkswap /dev/sdb2
swapon /dev/sdb2
```

---

## 🔹 Disable swap

```bash
swapoff -a
```

---

## 🔹 Swappiness (how aggressively swap is used)

```bash
cat /proc/sys/vm/swappiness
sysctl vm.swappiness=10
```

Persistent:

```bash
vm.swappiness=10 >> /etc/sysctl.conf
```

---

# 3️⃣ Filesystem Automounter (autofs)

---

## 🔹 Install

```bash
dnf install autofs -y
```

---

## 🔹 Master file

```bash
/etc/auto.master
```

Example:

```bash
/misc  /etc/auto.misc
```

---

## 🔹 Map file

```bash
/etc/auto.misc
```

```bash
data  -fstype=ext4  :/dev/sdb1
```

---

## 🔹 Start service

```bash
systemctl enable --now autofs
```

Test:

```bash
ls /misc/data
```

👉 Mount happens **on access only**

---

# 4️⃣ Remote Filesystems & Network Block Devices

---

## 🔹 NFS (Client)

### Install

```bash
dnf install nfs-utils
```

### Temporary mount

```bash
mount -t nfs server:/exports/data /mnt/nfs
```

### Persistent

```bash
server:/exports/data /mnt/nfs nfs defaults 0 0
```

---

## 🔹 CIFS / Samba

```bash
mount -t cifs //server/share /mnt/cifs -o username=user
```

Persistent:

```bash
//server/share /mnt/cifs cifs credentials=/root/.smbcred 0 0
```

---

## 🔹 Network Block Device (NBD)

```bash
modprobe nbd
nbd-client server 10809 /dev/nbd0
```

```bash
mkfs.ext4 /dev/nbd0
mount /dev/nbd0 /mnt/nbd
```

Disconnect:

```bash
nbd-client -d /dev/nbd0
```

---

# 5️⃣ Virtual File System (VFS)

---

## 🔹 Key idea

👉 **VFS = abstraction layer for all filesystems**

---

## 🔹 Important virtual FS

| FS      | Purpose               |
| ------- | --------------------- |
| `/proc` | process + kernel info |
| `/sys`  | hardware + kernel     |
| `/dev`  | device nodes          |
| `tmpfs` | RAM-based FS          |

---

## 🔹 Examples

```bash
mount -t proc proc /proc
mount -t tmpfs tmpfs /mnt/tmp
df -Th
```

Persistent tmpfs:

```bash
tmpfs /mnt/tmp tmpfs defaults,size=512M 0 0
```

---

# 6️⃣ LVM – VERY IMPORTANT ⭐⭐⭐

---

## 🔹 LVM Flow (memorize this)

```
Physical Volume → Volume Group → Logical Volume → Filesystem → Mount
```

---

## 🔹 Create LVM

### 1. Create PV

```bash
pvcreate /dev/sdb
```

### 2. Create VG

```bash
vgcreate vgdata /dev/sdb
```

### 3. Create LV

```bash
lvcreate -L 5G -n lvdata vgdata
```

---

## 🔹 Format + Mount

```bash
mkfs.ext4 /dev/vgdata/lvdata
mount /dev/vgdata/lvdata /mnt/lvdata
```

Persistent:

```bash
/dev/vgdata/lvdata /mnt/lvdata ext4 defaults 0 2
```

---

## 🔹 Extend LVM (VERY COMMON EXAM TASK)

```bash
lvextend -L +2G /dev/vgdata/lvdata
resize2fs /dev/vgdata/lvdata
```

OR (one command):

```bash
lvextend -r -L +2G /dev/vgdata/lvdata
```

---

## 🔹 Reduce LVM (EXT4 only)

```bash
umount /mnt/lvdata
e2fsck -f /dev/vgdata/lvdata
resize2fs /dev/vgdata/lvdata 3G
lvreduce -L 3G /dev/vgdata/lvdata
mount /mnt/lvdata
```

---

## 🔹 Remove LVM

```bash
lvremove /dev/vgdata/lvdata
vgremove vgdata
pvremove /dev/sdb
```

---

## 🔹 View LVM info

```bash
pvs
vgs
lvs
```

---

# 7️⃣ Monitor Storage Performance

---

## 🔹 Disk usage

```bash
df -h
du -sh *
```

---

## 🔹 I/O performance

```bash
iostat
iotop
```

---

## 🔹 Disk details

```bash
lsblk
blkid
```

---

## 🔹 Heavy disk usage detection

```bash
find / -xdev -size +1G
```

---

# 🚀 FINAL LFCS MEMORY BOOST

### 🔑 Always remember:

```
mount     → temporary
fstab     → persistent
UUID      → safest
xfs       → grow only
ext4      → grow + shrink
LVM       → flexible storage
```

---

### 🧠 10-second recall flow:

```
Disk → Partition → FS → Mount
PV → VG → LV → FS → Mount
```

---

If you want next:
✅ **Ultra-short 1-page printable PDF**
✅ **Last-day 20 tricky LFCS storage questions**
✅ **Hands-on mini lab (30 mins practice)**

Just tell me 👍


---

# Questions:

Perfect 👍
I’ve created your **Ultra-Short 1-Page Printable LFCS Storage Cheat Sheet** and also prepared **20 tricky LFCS exam questions with answers separated** (as requested).

---

# 📄 ✅ Download – 1-Page LFCS Storage Cheat Sheet

👉 **[Download PDF](sandbox:/mnt/data/LFCS_Storage_1Page_CheatSheet.pdf)**

This is optimized for:

* Last-day revision
* Quick memory recall
* Exam terminal reference thinking

---

# 🧠 Part 1 — 20 Tricky LFCS Storage Questions

(Read first, **don’t scroll to answers immediately**)

---

### **Q1**

You created a filesystem and added it to `/etc/fstab`, but the system fails to boot. What command could have prevented this?

---

### **Q2**

Which command shows filesystem UUIDs required for persistent mounting?

---

### **Q3**

How do you temporarily mount an ext4 filesystem without affecting reboot behavior?

---

### **Q4**

Which filesystem can be grown but **not shrunk**?

---

### **Q5**

How do you safely extend an LVM logical volume and filesystem together?

---

### **Q6**

Which file controls automount behavior in Linux?

---

### **Q7**

What command shows swap usage in human-readable format?

---

### **Q8**

How do you make a swap file persistent across reboots?

---

### **Q9**

Which command shows disk I/O usage in real time?

---

### **Q10**

How do you identify which filesystem type is mounted on `/data`?

---

### **Q11**

Which filesystem is stored in memory (RAM)?

---

### **Q12**

How do you mount an NFS share temporarily?

---

### **Q13**

Which command lists Logical Volumes?

---

### **Q14**

How do you safely reduce an ext4 logical volume?

---

### **Q15**

What is the correct order of LVM layers?

---

### **Q16**

Which command helps find large files consuming space?

---

### **Q17**

How do you enable swap immediately after creating it?

---

### **Q18**

Which file defines permanent mounts?

---

### **Q19**

Which command verifies filesystem integrity?

---

### **Q20**

What is the safest identifier to use in `/etc/fstab`?

---

---

# ✅ Part 2 — Answers (For Final Revision)

---

### **A1**

```bash
mount -a
```

---

### **A2**

```bash
blkid
```

---

### **A3**

```bash
mount /dev/sdb1 /mnt
```

---

### **A4**

✅ **XFS**

---

### **A5**

```bash
lvextend -r -L +2G /dev/vg/lv
```

---

### **A6**

```bash
/etc/auto.master
```

---

### **A7**

```bash
free -h
```

---

### **A8**

Add to `/etc/fstab`:

```bash
/swapfile none swap sw 0 0
```

---

### **A9**

```bash
iotop
```

---

### **A10**

```bash
df -Th
```

---

### **A11**

```text
tmpfs
```

---

### **A12**

```bash
mount -t nfs server:/share /mnt
```

---

### **A13**

```bash
lvs
```

---

### **A14**

```bash
umount → e2fsck → resize2fs → lvreduce
```

---

### **A15**

```
PV → VG → LV → FS → Mount
```

---

### **A16**

```bash
find / -xdev -size +1G
```

---

### **A17**

```bash
swapon /swapfile
```

---

### **A18**

```bash
/etc/fstab
```

---

### **A19**

```bash
fsck
```

---

### **A20**

✅ **UUID**

---

# 🎯 Final Advice for Exam Day

* Always run `mount -a` after editing `/etc/fstab`
* Prefer **UUID** over device names
* Use **lvextend -r** to save time
* Remember: **XFS ≠ shrink**
* Don’t panic — commands are logical

---

If you want next:
✅ **30-minute rapid practice lab**
✅ **Printable command-only PDF**
✅ **Mock LFCS storage troubleshooting scenario**

Just tell me 👍
