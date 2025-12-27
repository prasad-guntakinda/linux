
## Q1. PASS
Configure the system to use the following NTP pools:

0.europe.pool.ntp.org
1.europe.pool.ntp.org
Next, change the timezone of this system to Europe, Bucharest.

Is the NTP pools added?

Is the time zone changed to Europe, Bucharest.?

## Q2: FAIL

Add a cron job for the user called john. Don't use the system-wide crontable, but rather add it to the personal crontable of the user called john.

Make sure that this cron job runs every Wednesday at 4AM. The command it should execute is find /home/john/ -type d -empty -delete.


Switch back to the bob user once the task is done.

Is the cron job added?

### Ans;
```
1) Switch to user john

sudo su john

2)Edit the crontab using crontab -e command as shown below to run jobs every Wednesday at 4 AM:

0 4 * * 3 find /home/john/ -type d -empty -delete
``` 

WRONG:
```
sudo crontab -u john -e
* 4 * * wed find /home/john/ -type d -empty - delete
```
## Q3: PASS

There is a network interface on this system which has the IP address 10.5.5.2 associated with it. What is the name of this network interface? Create a file in /opt/interface.txt and add a single line to it containing the exact name of that interface.


Is the name of the network interface stored in /opt/interface.txt?

---

## Q4: PASS

An administrator added a new user called jane to this system. But a few mistakes were made. Fix the following problems:

The administrator wanted to allow jane to run sudo commands. But instead of adding "jane" to the secondary/supplemental "sudo" group, the administrator changed the primary group to sudo. Fix this by doing the following: Set the primary/login group back to the group called jane. And add the user to the secondary/supplemental group called sudo. In essence the primary group for the user called "jane" should be "jane". And the secondary group should be "sudo".

Currently, the home directory path for the jane user is set correctly. But the directory itself is missing. Fix this by creating the /home/jane/ directory. Make sure that the directory is owned by the jane user and jane group.

The default shell for the user called jane is set to /bin/sh. Change the default shell to /bin/bash.

Finally, set the password for jane to 1234.


Is jane's primary group changed to jane?

Is jane's secondary group changed to sudo?

Is default shell changed to "/bin/bash"?

Is home directory created for jane?

---

## Q5: FAIL

Perform the following tasks:

Set up a port redirection rule that does the following: it redirects any IPv4 packet from the 10.5.5.0/24 CIDR range incoming on port 81 to another machine with the IP address 192.168.5.2 on port 80. To simplify this task, you are not required to specify input or output network interfaces.

Don't forget to add the proper masquerading rule so that packets redirected from 10.5.5.0/24 have a way of getting back to that sender, by our machine sitting in the middle and acting as an ad-hoc router between those two networks.

Make sure that after you add the rules you make them persistent (so that when the machine is rebooted these changes are not lost).


Is redirection rule added?

Is masquerading rule added?

Is the iptables rules persistent?


## Hint: 

Port redirection: Use the -t nat -A PREROUTING chain with the --dst, --dport, and -j DNAT target to redirect traffic.
Masquerading rule: In the POSTROUTING chain, add a rule for -s 10.5.5.0/24 to -j MASQUERADE.
Make rules persistent: Install iptables-persistent and save the rules with netfilter-persistent save.

### Solution:

Solution
1) Set Up Port Redirection Rule:

sudo iptables -t nat -A PREROUTING -p tcp -s 10.5.5.0/24 --dport 81 -j DNAT --to-destination 192.168.5.2:80

2) Add the masquerading rule:

sudo iptables -t nat -A POSTROUTING -s 10.5.5.0/24 -j MASQUERADE

3) To make iptables rules persistent:

sudo apt install iptables-persistent

---




## Q6: PASS

In /home/bob/certs/ directory you will find 4 files. That's because we generated two self-signed TLS certificates for you. Delete the 2 files containing the private keys. But preserve the certificate files.

At this point you're left with 2 files containing 2 separate certificates. They both use the RSA algorithm. But one is using 2048 bits for its cryptography purposes, while the other is using 4096. Delete the certificate that is using 2048 bits.


Is the certificate that is using 4096 bits still exists?

---

## 7: PASS

There is a file at /opt/aclfile. Currently no one has permissions to read, write, or execute this file, not even root. But instead of working with regular permissions, use ACL for this task. Add the following to the access control list:

The user called janet should be able to read and write to /opt/aclfile. Just read and write, no execute permission for this ACL entry.


Is read and write access given to janet?

---


## 8: PASS

Add two security limits to the configuration of this system:

The user called janet should have a hard limit so that she can't open more than 100 processes.

The group called mail should have a soft limit so users in the group not be able to create files larger than 8192 kilobytes.


Is limits configured to janet?

Is limits configured to mail group?


---

## 9: PASS

Weight: 7
In your home directory you will find a subdirectory called project. Navigate to it and then do the following:

Add file1 to the staging area to prepare it for a future commit.
Commit this file with the exact following message: Created first required file.
Now upload your changes to the remote repository already associated with your local repository. Everything is already set up for you. Use the remote repository aliased as origin and upload the master branch and password to push the code is C0ntr0lplan3Pa$$wd.

Is file1 created?

Is the changes committed?

Is the changes pushed to remote repo?


Ans:

```cd project/
   24  ls
   25  touch file1
   26  add file1 
   27  git add file1 
   28  ls 
   29  git commit -m 'Created first required file'
   30  git log
   31  git push origin
   32  git push -u origin master
```
---

## 10: FAIL

Perform the following tasks related to SELinux on node01:

First, check if SELinux is running in enforcing, permissive, or disabled mode. Create the file /opt/selinuxmode.txt. And write your answer to that file. Just one line where you write a single word: enforcing, permissive, or disabled, according to the status you found.

There is a file that has the wrong SELinux type label. Please correct that and restore the default SELinux label for the file at /usr/bin/less.


Credentials to access node01:
Name: bob
Password: caleston123

Is the status of SELinux stored at /opt/selinuxmode.txt?

Is the correct SELinux type label configured for /usr/bin/less?




### Hint/Solution:

1. Check SELinux status, write the mode to /opt/selinuxmode.txt:
Run:
  getenforce

Write the output ("Enforcing", "Permissive", or "Disabled") into /opt/selinuxmode.txt:
  echo "$(getenforce)" > /opt/selinuxmode.txt

2. Fix the SELinux label for /usr/bin/less:
To restore the default label, run:
  restorecon -v /usr/bin/less

Additional info:
To check if SELinux is "Enforcing," "Permissive," or "Disabled," simply run:
  getenforce

To verify the label change:
  ls -Z /usr/bin/less

Follow these steps, and you'll have both tasks done perfectly! Need more help?


### Solution:

Solution
1) Check the current SELinux status on node01 and store it in file /opt/selinuxmode.txt:

getenforce | sudo tee /opt/selinuxmode.txt

2) Correct SELinux Type Label for /usr/bin/less:

sudo restorecon -v /usr/bin/less



---

## Q11: PASS

Weight: 4
Nginx is installed but it's not running on caleston-lp10. Make the necessary changes so that:

Nginx is started immediately.
And also, Nginx will start up automatically every time the system boots up.
After starting Nginx, it has spawned at least 3 processes. We are not interested in the master process, only the worker processes. Under what username are these worker processes running? Create a new file at /opt/nginxuser.txt and add a single line to it with that username, other than bob.


Is the nginx started and enabled?

username stored at /opt/nginxuser.txt?


### ANs:
ob@caleston-lp10:~/project$ ps aux | grep nginx
root       11050  0.0  0.1  55232  1680 ?        Ss   18:13   0:00 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
www-data   11051  0.0  0.5  55868  5500 ?        S    18:13   0:00 nginx: worker process
bob        11064  0.0  0.2   6480  2360 pts/3    S+   18:16   0:00 grep --color=auto nginx
bob@caleston-lp10:~/project$ sudo vi /opt/nginxuser.txt


sudo systemctl enable --now nginx.service
   48  systemctl status nginx.service
   49  ps -ef | grep nginx
   50  pstree 
   51  ps aux 
   52  ps aux | grep nginx
   53  sudo vi /opt/nginxuser.txt



---


## Q12: FAIL

Weight: 5
A basic LVM structure exists on the node01. Make some changes to it:

The volume group called volume1 currently only includes /dev/vdb. Add /dev/vdc to volume1.
We have a logical volume called lv1. Resize this logical volume to 2GB.


Credentials to access node01:
Name: bob
Password: caleston123

Is /dev/vdc to volume1?

Is lv1 resized to 2GB?


### Solution:

Solution
1) Add /dev/vdc to volume1:

sudo vgextend volume1 /dev/vdc

2) Resize the Logical Volume lv1 to 2GB:

sudo lvresize --size 2G /dev/volume1/lv1



---

## Q13: PASS

Weight: 8
Perform the following tasks on node01:

Install Git.

In your home directory, download the entire repository from https://github.com/htop-dev/htop. By default, this action should create a new subdirectory called htop where all the project's files are located.

Switch to the htop subdirectory. Follow the project's instructions to build (compile) the htop application.

Install the newly built htop application. By default, the application should be installed in /usr/local, in the bin subdirectory


Credentials to access node01:
Name: bob
Password: caleston123

Switch back to caleston-lp10 once the task is done


---

## Q14: FAIL

Weight: 9
Create a virtual machine with the following parameters on caleston-lp10:

For the operating system information parameter use the ubuntu22.04.
Name the virtual machine mockexam2.
Assign 1024 MB of RAM to this machine.
Assign it one virtual CPU.
Import the disk image /var/lib/libvirt/images/ubuntu.img to this virtual machine.
At the end of your command, you can add the parameter --noautoconsole to avoid waiting for this virtual machine to boot up, and not get stuck in the virtual console after it initializes.
After you create this virtual machine, run a separate command to make mockexam2 automatically start up every time the system boots up.


Is mockexam2 configured to automatically start up?

Is mockexam configured to use 1 vCPU?

Is mockexam configured to use 1024 MB of RAM?


### Solution:

Solution
Run the below command to launch vm with ubuntu22.04 image:

virt-install \
--name mockexam2 \
--ram 1024 \
--vcpus=1 \
--os-variant=ubuntu22.04 \
--import \
--disk path=/var/lib/libvirt/images/ubuntu.img \
--noautoconsole

Configure the virtual machine to start automatically on boot:

virsh autostart mockexam2


---

## Q15: FAIL

Weight: 8
Create a network bridge between these network interfaces: [ "eth1", and "eth2"] on node01. Call the bridge bridge1. Turn DHCP off for IPv4 for both interfaces. However, for the bridge itself, turn DHCP on for IPv4.


Credentials to access node01:
Name: bob
Password: caleston123

Is bridge1 created?

Is eth1 added to bridge netwok?

Is eth2 added to bridge netwok?

### Solution:

Solution
To create a bridge network with eth1 and eth2, create a file /etc/netplan/99-bridge.yaml and edit the file as below:

network:
  version: 2
  renderer: networkd
  ethernets:
    eth1:
      dhcp4: no
    eth2:
      dhcp4: no
  bridges:
    bridge1:
      dhcp4: yes
      interfaces:
        - eth1
        - eth2

Apply the new network configuration using the netplan apply command


---


## Q16: FAIL

Weight: 14
Perform the following tasks on node01:

Remove the Docker image called nginx.

Start a container based on the httpd image. Name it apache_container. Instruct Docker to redirect connections coming to port 80 on the host to port 80 of this container. Also instruct Docker to restart this container only on failure, with a maximum number of retries set to 3 (you will have to look through the correct manual to find the parameter you need).


Credentials to access node01:
Name: bob
Password: caleston123

Switch back to caleston-lp10 once the task is done

Is the nginx image removed?

Is the apache_container created with required configuration?


### Solution:

1) Delete the containers using nginx image:

docker rm nginx_container

2) Delete the nginx image:

docker rmi nginx

3) Run the below command to create httpd container:

docker run --detach --publish 80:80 --name=apache_container --restart=on-failure:3 httpd

---


## Q17: FAIL

Weight: 5
The `node01` was configured to use LDAP entries from a certain server. However, some configuration options are wrong. Edit the correct configuration files and fix the following mistakes:

Our name service local daemon is configured to look for an LDAP server at the wrong IP address (currently 10.9.9.8). Fix this and configure the correct IP to find it run ip -4 addr show eth0 ignoring /24 . Make sure your changes become active after you edit the configuration file.

Our system is currently configured to get group data and password data from the LDAP server but not user data. Configure it to get user data as well.


Credentials to access `node01`:
Name: bob
Password: caleston123

Switch back to caleston-lp10 once the task is done

Is the ldap address updated?

Is ldap configuration updated to get user data?

Is the status of nslcd active?



### Solution:

1. Edit `/etc/nslcd.conf` to change the ldap server:

```ini
# The user and group nslcd should run as.
uid nslcd
gid nslcd

# The location at which the LDAP server(s) should be reachable.
uri ldap://192.168.121.167/ # updated it with the output of ``ip -4 addr show eth0`

# The search base that will be used for all queries.
base dc=example,dc=org

# The LDAP protocol version to use.
#ldap_version 3

# The DN to bind with for normal lookups.
#binddn cn=annonymous,dc=example,dc=net
#bindpw secret

```

2. Edit `/etc/nsswitch.conf` to get user data:

```ini
# /etc/nsswitch.conf
#
# Example configuration of GNU Name Service Switch functionality.
# If you have the `glibc-doc-reference' and `info' packages installed, try:
# `info libc "Name Service Switch"' for information about this file.

passwd:         files systemd ldap #updated
group:          files systemd ldap
shadow:         files ldap
gshadow:        files
```

- Restart the nslcd service:
```bash
sudo systemctl restart nslcd
```
---


---
---

# Retrospection:

- Time Management: 
    - spent more time for small tasks to review the man pages. Need to speed up
    - At the end more weightage questions came but no time to finish it off

## Essential Commands:
- Schedulers: cron

## Users and Groups:
- usermod command
- LDAP Server configuration

## Operations Deployment:
- Docker Containers
- SELinux
- Configure and manage VMs


## Networking 
- Port Redirection and NAT with persistent configuration
- Bridge
- Bond
- Yaml config files

## Storage:
- LVM
- Mounting Options






