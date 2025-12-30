
## Q1:

Weight: 6
A process mockexam.sh is running under the user account called bob. Find and terminate that process.


Is the process terminated?


---

## Q2:

Weight: 7
Nginx is installed and a configuration file is available to make it work as a load balancer. However, some steps are still required:

Edit the provided configuration file and add another server for load balancing.
The server's IP is 10.9.0.3. Assign it a weight equal to 3 so that requests are sent to this server more often.
Take the rest of the steps necessary to make this new configuration active for Nginx.

Is the third server line added with weight 3?

---


## Q3: 

Weight: 6
A user called jane exists on this system. Make the following changes:

Change the default shell of this user to /bin/bash.
Add the user to an additional secondary group called sudo. Make sure to preserve the secondary groups jane is already part of.

Is the user "jane" added to the "sudo" group?

Is the default shell of "jane" set to "/bin/bash"?

---

## Q4:

Weight: 6
Add a cron job to the crontable of the user called john (not the system-wide crontable).

This cron job should be executed every Monday and Saturday at 3 AM (03:00).
The command executed by this cron job should be:
tar acf /home/john/www-snapshot.tgz /var/nginx

Important note: The crontable entry should be specified on a single line (not one line for Monday and a separate one for Friday).

Is the cron job scheduled correctly for user "john"?

---


## Q5:

Weight: 5
We've created a configuration file that defines a network bond between two interfaces. But we haven't activated it yet.

Change that configuration file to make eth3 the primary interface of this network bond.
Activate the new network settings defined in that file, so that the network bond gets created accordingly.

Is eth3 set as the primary interface?


---

## Q6:

Weight: 12
Perform the following tasks:

Set up a port redirection rule. All connections coming from the 10.9.9.0/24 IP range, on port 8080 should be redirected to 10.100.0.8 on port 80.
Make sure to also set up masquerading so that traffic can also be returned to the sender (10.9.9.0/24).

Is the port redirection rule set up correctly?

Is the masquerading rule set up correctly?

---


## Q7:

Weight: 6
The systemd-timesyncd package is already installed. However, automatically setting up the correct time by using NTP servers is currently disabled.

Enable NTP time synchronization on this machine.
Then set the timezone to Asia, Singapore.
Finally, Determine the name of the fallback NTP pool currently configured for this system. Extract only the name of the pool (e.g., "example.ubuntu.com") and create the file /opt/pool.txt containing that name on a single line.

Is the fallback NTP pool saved correctly?

---


## Q8:

Weight: 8
In your home directory, you will find a subdirectory called project1. Inside, a local Git repository was already set up, initialised, and associated with a remote repository aliased as "origin".

Stage and commit all files in the project1 repository.
Use the commit message: Initial commit of project1.
Push your changes to the master branch of the remote repository.

Is the commit message correct?

---


## Q9:

Weight: 4
In your /home/bob/certs directory, you will find six files. Three private keys and three TLS certificates that are associated with those keys. However, two certificates have incorrect data.

Look for the certificate generated for kodekloud.com.
Keep that certificate and its associated key.
Delete the two certificates that aren't associated with kodekloud.com. Also, delete their corresponding keys.
At the end of this task, you should be left with two files: the certificate for "kodekloud.com" and its associated key.

Are only the correct certificate and key remaining?

---

## Q10:

Weight: 5
The Logical Volume Manager was used to create the following structure:

A volume group called VG1.
A logical volume called LV1.
On this logical volume we have an ext4 filesystem of approximately 2GB.
Expand the logical volume called LV1 to use 100% of the free space available on the VG1 volume group.

Is all free space used in VG1?


---


## Q11:

Weight: 5
We have a Docker container on this system(node01) that is currently stopped. Find that container and delete it.


Note: Perform this task on node01. Access node by ssh node01 password caleston123

Is the stopped Docker container deleted?

---

## Q12:

Weight: 6
Create an ext4 filesystem on /dev/vdc. Then configure the system to automatically mount this new filesystem in the /home/bob/share directory every time it boots up.


Note: Exit from node01 if you are in node01

Is the /home/bob/share directory mounted to /dev/vdc?

---


## Q13:

Weight: 5
Configure the system to automatically adjust the value of a certain kernel parameter every time it boots up.
net.ipv6.conf.all.forwarding should be set to 1 at each boot.


Is the kernel parameter set correctly?

---


## Q14:

Weight: 6
The /var/www/ directory and all of its content is currently labeled like this under SELinux:
unconfined_u:object_r:var_t:s0

Change the label on the /var/www/ directory itself, its subdirectories, and all the files it contains.

More specifically, change the SELinux type on these objects to httpd_sys_content_t.

At the end of this task, everything in /var/www/ (and the directory itself) should end up with the following label:

unconfined_u:object_r:httpd_sys_content_t:s0


Is the SELinux type changed correctly?

---

## Q 15:

Weight: 5
In the /opt/archive/ directory there is a single file with the permission to be executed by its user. Find the user's executable file and delete it.


Is the user executable file deleted?

---


## Q16:

Weight: 5
Find the filesystem that is almost full (100% of available space is used, or very close to 100%). Free up some space by deleting the largest file from that filesystem.


Is the largest file deleted?

---

## 17:

Weight: 3
Swap is currently used on this system. Disable (turn off) only swap with name additionalspace.


Is swap turned off?

---

---
---

Below is your **FULL LFCS Mock Exam – Question & Answer format**, written **cleanly, exam-style**, and aligned with **real LFCS expectations**.
You can revise this quickly before the exam.

---

# ✅ **LFCS MOCK EXAM – QUESTIONS & ANSWERS**

---

## **Q1 – Kill a running process**

### **Question**

A process `mockexam.sh` is running under user **bob**.
Find and terminate the process.

---

### **Answer**

```bash
ps -u bob | grep mockexam.sh
```

Find PID, then terminate:

```bash
kill <PID>
```

If still running:

```bash
kill -9 <PID>
```

Verify:

```bash
ps -u bob | grep mockexam.sh
```

✅ **Process terminated**

---

## **Q2 – Nginx Load Balancer Configuration**

### **Question**

Add a backend server with IP `10.9.0.3` and weight `3`, then reload nginx.

---

### **Answer**

Edit config file (usually):

```bash
sudo vi /etc/nginx/conf.d/loadbalancer.conf
```

Add inside `upstream` block:

```nginx
server 10.9.0.3 weight=3;
```

Test config:

```bash
sudo nginx -t
```

Reload nginx:

```bash
sudo systemctl reload nginx
```

✅ **Server added with weight 3**

---

## **Q3 – User Management (jane)**

### **Question**

Change shell to `/bin/bash` and add user to `sudo` group.

---

### **Answer**

```bash
sudo usermod -s /bin/bash jane
sudo usermod -aG sudo jane
```

Verify:

```bash
getent passwd jane
groups jane
```

✅ **Shell & sudo group updated**

---

## **Q4 – Cron Job for User john**

### **Question**

Run command every **Monday and Saturday at 03:00**.

---

### **Answer**

Edit john’s crontab:

```bash
sudo crontab -u john -e
```

Add:

```bash
0 3 * * 1,6 tar acf /home/john/www-snapshot.tgz /var/nginx
```

Verify:

```bash
sudo crontab -u john -l
```

✅ **Cron job configured correctly**

---

## **Q5 – Network Bond Primary Interface**

### **Question**

Set **eth3** as primary interface in bond and activate it.

---

### **Answer**

Edit bond config:

```bash
sudo vi /etc/sysconfig/network-scripts/ifcfg-bond0
```

Add or modify:

```bash
BONDING_OPTS="mode=active-backup primary=eth3"
```

Restart network:

```bash
sudo nmcli connection reload
sudo nmcli connection up bond0
```

✅ **eth3 is primary interface**

---

## **Q6 – Port Forwarding + Masquerading**

### **Question**

Redirect traffic from `10.9.9.0/24:8080` → `10.100.0.8:80`

---

### **Answer**

Enable IP forwarding:

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

Add NAT rules:

```bash
sudo iptables -t nat -A PREROUTING -s 10.9.9.0/24 -p tcp --dport 8080 -j DNAT --to-destination 10.100.0.8:80
```

Enable masquerading:

```bash
sudo iptables -t nat -A POSTROUTING -s 10.9.9.0/24 -j MASQUERADE
```

Verify:

```bash
sudo iptables -t nat -L -n -v
```

✅ **Port redirection & masquerading active**

---

## **Q7 – NTP Configuration**

### **Question**

Enable NTP, set timezone, and save fallback pool.

---

### **Answer**

Enable NTP:

```bash
sudo timedatectl set-ntp true
```

Set timezone:

```bash
sudo timedatectl set-timezone Asia/Singapore
```

Find fallback pool:

```bash
grep -i fallback /etc/systemd/timesyncd.conf
```

Save pool:

```bash
echo "ntp.ubuntu.com" > /opt/pool.txt
```

✅ **Fallback NTP saved**

---

## **Q8 – Git Commit & Push**

### **Question**

Commit and push all files in `project1`.

---

### **Answer**

```bash
cd ~/project1
git add .
git commit -m "Initial commit of project1"
git push origin master
```

✅ **Commit message correct**

---

## **Q9 – TLS Certificates Cleanup**

### **Question**

Keep only `kodekloud.com` cert + key.

---

### **Answer**

```bash
cd /home/bob/certs
openssl x509 -in *.crt -noout -subject
```

Identify correct cert, then:

```bash
rm wrongcert1.crt wrongkey1.key
rm wrongcert2.crt wrongkey2.key
```

Verify:

```bash
ls
```

✅ **Only correct cert & key remain**

---

## **Q10 – Extend LVM Volume**

### **Question**

Extend LV1 to use all free VG space.

---

### **Answer**

```bash
sudo lvextend -l +100%FREE /dev/VG1/LV1
sudo resize2fs /dev/VG1/LV1
```

Verify:

```bash
vgs
```

✅ **VG fully used**

---

## **Q11 – Remove Stopped Docker Container**

### **Answer**

```bash
ssh bob@node01
docker ps -a
docker rm <container_id>
```

Verify:

```bash
docker ps -a
```

✅ **Stopped container removed**

---

## **Q12 – Mount /dev/vdc to /home/bob/share**

### **Answer**

```bash
sudo mkfs.ext4 /dev/vdc
sudo mkdir -p /home/bob/share
sudo mount /dev/vdc /home/bob/share
```

Persist:

```bash
echo "/dev/vdc /home/bob/share ext4 defaults 0 0" | sudo tee -a /etc/fstab
```

Verify:

```bash
mount | grep vdc
```

✅ **Mounted correctly**

---

## **Q13 – Kernel Parameter Persistent Change**

### **Answer**

```bash
echo "net.ipv6.conf.all.forwarding=1" | sudo tee /etc/sysctl.d/99-ipv6-forward.conf
sudo sysctl -p /etc/sysctl.d/99-ipv6-forward.conf
```

Verify:

```bash
sysctl net.ipv6.conf.all.forwarding
```

✅ **Parameter applied**

---

## **Q14 – SELinux Context Change**

### **Answer**

```bash
sudo semanage fcontext -a -t httpd_sys_content_t "/var/www(/.*)?"
sudo restorecon -Rv /var/www
```

Verify:

```bash
ls -Z /var/www
```

✅ **SELinux label updated**

---

## **Q15 – Delete User Executable File**

### **Answer**

```bash
find /opt/archive -type f -perm -u=x
```

Delete:

```bash
rm <filename>
```

✅ **Executable removed**

---

## **Q16 – Free Space on Full Filesystem**

### **Answer**

```bash
df -h
cd /mount_point
du -sh * | sort -hr
```

Delete largest file:

```bash
rm largest_file
```

✅ **Disk space freed**

---

## **Q17 – Disable Swap `additionalspace`**

### **Answer**

```bash
sudo swapoff /dev/mapper/additionalspace
```

Verify:
```bash
swapon --show
```

✅ **Swap disabled**

---

# 🎯 **ALL TASKS COMPLETED SUCCESSFULLY**

---

If you'd like next:
✅ **One-page revision sheet**
✅ **Command-only cheat sheet**
✅ **Timed LFCS mock test**

Just tell me 👍
