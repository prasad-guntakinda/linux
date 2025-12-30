# Test-1



### 1. In the /opt/findme/ directory you will find 10,000 files. You need to filter out specific files

Find all files that have the executable permission bit enabled for the user that owns them. Redirect the output of the find command to the /opt/foundthem.txt file.
Find all files that have the SETUID permission enabled and delete them.
Find any file that is larger than 1KB and copy it to the /opt/ directory




---


### 2. Perform the following two tasks:

Create a bash script that recursively copies the /var/www/ directory into the /opt/www-backup/ directory

Save your script at /opt/script.sh. Remember, the script file you create also has to be executable.

Make sure that your script /opt/script.sh automatically runs every day at 4AM. More specifically, create a cron job that runs that script every day at 4AM. Put this in the system-wide cron table (not root's local cron table) and make sure the script executes under the root user.


---


### 3.Enforce some limits on two users:

Set a limit on the user called john so that he can open no more than 30 processes. This should be a hard limit.

For the user called jane make sure she can create files not larger than 1024 kilobytes. Make this a soft limit.


---


### 4. Create a new user on this system called mary

Set her password to 1234.
Leave the full name and other personal details empty.
Set her default shell to /bin/dash.
Make sure she can execute sudo commands by adding her to the secondary group called sudo.
At this point Mary's primary group is mary. And her secondary group is sudo. Change her primary group to developers. Without affecting her secondary group.




---


### 5. Modify the following kernel runtime parameter:

vm.swappiness set it to a value of 10. This should be a persistent change, added to a file so that vm.swappiness is set to 10 every time the system boots up. However, after you create the proper file, also set this runtime parameter to 10 for this session as well. Otherwise said, the file will set the parameter to 10 the next time the system boots up, but we want to set it to 10 even for this current, active session, instead of waiting until the next boot until that takes effect.


---

### 6. You have an xfs filesystem on /dev/vdb1. Also, there's an ext4 filesystem on /dev/vdb2.

Edit the correct file in /etc/ so that /dev/vdb1 is automatically mounted into the /backups directory every time the system boots up. Default mount options should be used.

/dev/vdb2 is already mounted in /mnt/. But there is a problem. Sensitive data exists on this ext4 filesystem and you want to make sure that it's not accidentally modified. To solve this problem, remount /dev/vdb2 into the /mnt directory, but this time, with the read-only mount option. It does not matter what the other mount options are. Just make sure this mount point is read-only so that users cannot change contents on this filesystem.



---

### 7. Use the Logical Volume Manager to perform the following tasks:

Add /dev/vdc and /dev/vdd as Physical Volumes to LVM.
Create a Volume Group on these two physical volumes. Call the volume volume1.
On the Volume Group called volume1 create a new Logical Volume. Call this Logical Volume website_files. Set the size of the Logical Volume to 3GB.



---


### 8. In your home directory you will find a subdirectory called kode. Git tools are pre-installed. Switch to the kode subdirectory and perform the following tasks:

Initialize this subdirectory as an empty Git repository.
Associate this local Git repository with the remote repository found at https://github.com/kodekloudhub/git-for-beginners-course.git. Add this as a remote repository and call it (alias it as) origin.
Download all the latest changes from the master branch from that remote repository into your local repository.





---


### 9. A Docker container is running on node01. Perform the following tasks:

Stop and remove the container that is currently running, since it's not configured correctly.
In your home directory you will find a subdirectory called kode_web. It contains all the necessary build instructions for Docker. Use that directory to build a new Docker image. Call this image kodekloudwebserv.
Finally, launch a container based on the kodekloudwebserv image. In your command, make sure that all connections incoming to port 8081 on the host are redirected to port 80 of the container. Call this container webserver2.

Credentials to access node01:
Name: bob
Password: caleston123



---


### 10. NFS server and client tools are installed on caleston-lp10 system. Instruct the NFS server to share the /home directory in read-only mode with IP addresses in the 10.0.0.0/24 CIDR range.


---


### 11. Find the application that is accepting incoming connections on port 80. Make note of the exact name of that application. You will need it later on.

As you investigated what application is accepting incoming connections to port 80, you might have noticed that two or more PIDs are associated with that. Basically, the application has forked multiple processes to do its job. Figure out which PID is associated with the master process.

With both of these things noted, create the following file: /opt/process.txt.

On the first line add the name of the application associated with port 80, in all lowercase letters.
On the second line add the PID number associated with the master process.



---


### 12. Explore your network settings and perform the following tasks:

There is currently one network interface which does not have any IPv4 address associated with it. Temporarily assign it the following IPv4 address: 10.5.0.1/24. This should not be a permanent change (no need to edit configuration files).

What is the default route for this system? Create a file, and add a single line where you save the IP address for the gateway used by this default route (i.e., requests are routed to what IP address?). Save the address in this file: /opt/gateway.txt.

For the final task, find out what is the IP address of the main DNS resolver configured for this system. Create the file /opt/dns.txt and add a single line to it with that IP address.



---


### 13. There is a another virtual machine with name node01 is available for you. Perform this task on node01. You can access the node by ssh

username: bob
Host- node01
Password- caleston123 

You can identify the disk because it is currently unpartitioned, not mounted anywhere, and is 2 GB in size.
Disk /dev/vdb is unpartitioned, not mounted anywhere, and is 2 GB in size.

Create two partitions of equal size on this disk: 500M each. Ignore about remaining 1GB partition.
Create an ext4 filesystem on the first partition.
Create an xfs filesystem on the second partition.
Create two directories: /part1 and /part2.
Manually mount the ext4 filesystem in the /part1 directory. Manually mount the xfs filesystem in the /part2 directory.
Also, configure the system to automatically mount these the same way, every time the operating system boots up.



---


### 14. Configure the system to use /swfile as a swap file on node01.

First, create /swfile. Make the size of this file exactly 1024 MB.
Then take all the necessary steps to temporarily mount this as swap. (So that it's immediately used as swap for this current boot session).
But also make sure to configure the system to also use this as swap every time it will boot up in the future.

Credentials to access node01:
Name: bob
Password: caleston123



---


### 15. On node01two processes are overusing our storage device. One is executing a lot of I/O operations per second (small data transfers, but a very large number of such transfers). Otherwise said, the process has a high tps/IOPS. The other process is reading very large volumes of data.

Identify the process with the high tps. What partition is it using? Create the file /opt/devname.txtand write the device name of that partition inside that file. For example, if it's using /dev/vde5, you would simply write /dev/vde5 on a single line in that file. Note that there might be some abstractions behind this, and we're not interested in device mapper names, but rather, the real device the mapper is using.

Identify the process with the high read transfer rate/second. Create the file /opt/highread.pid and write the PID number of that process in that file. For example, if the PID is 3886 you just write 3886 in that file (only the number, on a single line).




---

### 16. 

On node01 list all filesystems to check out how much free space they have remaining. You'll find one which is almost full (should be around 98% full). To confirm it is the correct filesystem, see where it is mounted, and you should find many directories on it in the form of numbers from 1 to 999. Find the directory which has the largest file and delete that file (only that file, nothing else).


---


### 17. 
On caleston-lp10 change the configuration for the SSH daemon. Disable X11 forwarding globally. Then, make an exception for just one user called bob. For that user alone enable X11 forwarding.


Do not restart the SSH service after making the changes.


---


Perfect 👍
Below is your **LFCS Mock Exam – Question & Answer (Q&A) Format**, clean and exam-friendly, so you can **revise everything in one pass**.

---

# ✅ **LFCS Mock Exam – Question & Answer Format**

---

## **1. File Search & Permissions**

### **Q1. Find all files executable by the owner and save output to `/opt/foundthem.txt`.**

**A:**

```bash
find /opt/findme -type f -perm -u=x > /opt/foundthem.txt
```

---

### **Q2. Find all files with SETUID permission and delete them.**

**A:**

```bash
find /opt/findme -type f -perm -4000 -delete
```

---

### **Q3. Find all files larger than 1KB and copy them to `/opt`.**

**A:**

```bash
find /opt/findme -type f -size +1k -exec cp {} /opt/ \;
```

---

# **2. Script & Cron Job**

### **Q4. Create a script to copy `/var/www` to `/opt/www-backup`.**

**A:**

```bash
vi /opt/script.sh
```

```bash
#!/bin/bash
cp -r /var/www /opt/www-backup/
```

```bash
chmod +x /opt/script.sh
```

---

### **Q5. Schedule the script to run daily at 4 AM system-wide.**

**A:**

```bash
sudo vi /etc/crontab
```

Add:

```bash
0 4 * * * root /opt/script.sh
```

---

# **3. User Limits**

### **Q6. Limit user `john` to max 30 processes (hard limit).**

**A:**

```bash
sudo vi /etc/security/limits.conf
```

```bash
john hard nproc 30
```

---

### **Q7. Limit user `jane` to max file size of 1024 KB (soft limit).**

**A:**

```bash
jane soft fsize 1024
```

---

# **4. User Management**

### **Q8. Create user `mary` with `/bin/dash` shell.**

**A:**

```bash
sudo useradd -m -s /bin/dash mary
```

---

### **Q9. Set password to `1234`.**

**A:**

```bash
echo "mary:1234" | sudo chpasswd
```

---

### **Q10. Add mary to sudo group.**

**A:**

```bash
sudo usermod -aG sudo mary
```

---

### **Q11. Change mary’s primary group to `developers`.**

**A:**

```bash
sudo groupadd developers
sudo usermod -g developers mary
```

---

# **5. Kernel Parameter (Swappiness)**

### **Q12. Set `vm.swappiness=10` temporarily and permanently.**

**A:**

```bash
sudo sysctl -w vm.swappiness=10
```

```bash
echo "vm.swappiness=10" | sudo tee /etc/sysctl.d/99-swappiness.conf
```

Apply:

```bash
sudo sysctl -p /etc/sysctl.d/99-swappiness.conf
```

---

# **6. Filesystem Mounts**

### **Q13. Mount `/dev/vdb1` permanently on `/backups`.**

**A:**

```bash
sudo mkdir -p /backups
sudo vi /etc/fstab
```

Add:

```
/dev/vdb1  /backups  xfs  defaults  0 0
```

```bash
sudo mount -a
```

---

### **Q14. Remount `/dev/vdb2` as read-only.**

**A:**

```bash
sudo mount -o remount,ro /mnt
```

---

# **7. LVM Tasks**

### **Q15. Create physical volumes.**

**A:**

```bash
sudo pvcreate /dev/vdc /dev/vdd
```

---

### **Q16. Create volume group `volume1`.**

**A:**

```bash
sudo vgcreate volume1 /dev/vdc /dev/vdd
```

---

### **Q17. Create logical volume `website_files` (3GB).**

**A:**

```bash
sudo lvcreate -L 3G -n website_files volume1
```

---

# **8. Git Tasks**

### **Q18. Initialize repo and connect to remote.**

**A:**

```bash
cd ~/kode
git init
git remote add origin https://github.com/kodekloudhub/git-for-beginners-course.git
```

---

### **Q19. Pull latest changes from master.**

**A:**

```bash
git pull origin master
```

---

# **9. Docker Tasks**

### **Q20. Stop and remove running container.**

**A:**

```bash
docker ps
docker stop <container_id>
docker rm <container_id>
```

---

### **Q21. Build Docker image.**

**A:**

```bash
cd ~/kode_web
docker build -t kodekloudwebserv .
```

---

### **Q22. Run container on port 8081 → 80.**

**A:**

```bash
docker run -d -p 8081:80 --name webserver2 kodekloudwebserv
```

---

# **10. NFS Export**

### **Q23. Share `/home` as read-only for 10.0.0.0/24.**

**A:**

```bash
sudo vi /etc/exports
```

```
/home 10.0.0.0/24(ro,sync)
```

Apply:

```bash
sudo exportfs -rav
```

---

# **11. Process Listening on Port 80**

### **Q24. Identify application and PID.**

**A:**

```bash
ss -tulnp | grep :80
```

OR

```bash
lsof -i :80
```

Master PID:

```bash
ps -ef | grep <process>
```

---

### **Q25. Save results.**

```bash
echo "apache2" > /opt/process.txt
echo "1234" >> /opt/process.txt
```

---

# **12. Network Configuration**

### **Q26. Assign temporary IP.**

**A:**

```bash
sudo ip addr add 10.5.0.1/24 dev <interface>
```

---

### **Q27. Save default gateway.**

**A:**

```bash
ip route | grep default | awk '{print $3}' > /opt/gateway.txt
```

---

### **Q28. Save DNS server IP.**

**A:**

```bash
cat /etc/resolv.conf | grep nameserver | awk '{print $2}' > /opt/dns.txt
```

---

# **13. Disk Partitioning (node01)**

### **Q29. Create partitions on `/dev/vdb`.**

```bash
sudo fdisk /dev/vdb
```

Create:

* `/dev/vdb1` → 500MB
* `/dev/vdb2` → 500MB

---

### **Q30. Format partitions.**

```bash
sudo mkfs.ext4 /dev/vdb1
sudo mkfs.xfs /dev/vdb2
```

---

### **Q31. Mount partitions and persist.**

```bash
sudo mkdir /part1 /part2
sudo mount /dev/vdb1 /part1
sudo mount /dev/vdb2 /part2
```

```bash
sudo vi /etc/fstab
```

```
/dev/vdb1 /part1 ext4 defaults 0 0
/dev/vdb2 /part2 xfs  defaults 0 0
```

---

# **14. Swap File**

### **Q32. Create 1GB swap file and enable it.**

```bash
sudo fallocate -l 1024M /swfile
sudo chmod 600 /swfile
sudo mkswap /swfile
sudo swapon /swfile
```

---

### **Q33. Make swap persistent.**

```bash
echo "/swfile swap swap defaults 0 0" | sudo tee -a /etc/fstab
```

---

# **15. High Disk I/O Processes**

### **Q34. Identify high I/O process.**

```bash
iotop
```

### **Q35. Save device name.**

```bash
echo "/dev/vdb" > /opt/devname.txt
```

---

### **Q36. Save PID with high read.**

```bash
echo 3886 > /opt/highread.pid
```

---

# **16. Disk Space Cleanup**

### **Q37. Identify nearly full filesystem.**

```bash
df -h
```

### **Q38. Find largest file and delete it.**

```bash
cd /mount_point
du -sh * | sort -hr
rm largest_file
```

---

# **17. SSH Configuration**

### **Q39. Disable X11 globally, enable only for user bob.**

**A:**

```bash
sudo vi /etc/ssh/sshd_config
```

Add:

```bash
X11Forwarding no

Match User bob
    X11Forwarding yes
```

⚠️ **Do NOT restart sshd**

---

# ✅ DONE — LFCS EXAM READY 🎯

---

If you want next:
✅ **Printable 1-page revision sheet**
✅ **Command-only version**
✅ **Timed mock exam simulation**

Just tell me 👍


