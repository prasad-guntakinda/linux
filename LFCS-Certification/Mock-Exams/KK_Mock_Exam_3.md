
## Q1: FAIL
Weight: 6
Nginx is installed and a configuration file for running Nginx as a reverse proxy is already available. Perform the following tasks:

Disable the default Nginx website configuration.

Find the correct file where the reverse proxy configuration for Nginx is defined. Edit this file and make it redirect requests to http://google.com.

Make Nginx use this configuration file so that it starts working as a reverse proxy.


Is reverse proxy configured to redirect to google.com ?


### Solution
Disable the default nginx website configuration

sudo rm /etc/nginx/sites-enabled/default

File configured for running Nginx as proxy is proxy.conf available under /etc/nginx/sites-available. Edit the proxy.conf to redirect requests to google.com.

server {
    listen 80;
    location / {
        proxy_pass http://google.com;
        include proxy_params;
    }
}

Create a softlink to proxy.conf:

sudo ln -s /etc/nginx/sites-available/proxy.conf /etc/nginx/sites-enabled/proxy.conf

Reload the nginx service to pick up the changes.

sudo systemctl reload nginx.service

### Mistake forgot to create soft link

---

## Q2: FAIL

Weight: 4
Add a cron job to the system-wide cron table. This cron job should run as a user called Mary, on the 15th day of every month, at 4 AM*. The command that the cron job should execute is: find /home/mary/ -type d -empty -delete.


Is system-wide cron job is scheduled correctly for user mary ?

### Solution
Open the root crontab file:

sudo vim /etc/crontab

Add the cron job entry at the end of the file:

0 4 15 * * mary find /home/mary/ -type d -empty -delete

Save and exit the editor


### Mistakes 
- username kept as Mary
- cron started with `* 4 15` it should be `0 4` for 4:00AM

---

## Q3: PASS

Weight: 5
Make some changes to the network configuration settings of the interface called enp0s10 using netplan.

First, make a persistent change to the correct configuration file. There are two routes defined for our network interface. Remove the route that sends network packets to 192.168.1.0/24 through the server at 10.198.0.2. Make sure that your changes are applied.

Next, add a temporary IP address to our interface. Assign it the following IP: 10.5.0.9/24. No configuration files should be edited for this second task.


Making sure the 192.168.1.0/24 route is gone.

Is the mentioned interface has ip address 10.5.0.9/24 assigned?


---

## Q4: PASS

Weight: 5
Change the following kernel parameters:

Set vm.swappiness to 30. Make this a non-persistent change that will be lost at the next reboot.

Set vm.dirty_ratio to 15. Make this a persistent change so that it survives across reboots.


VM swapiness is set to 30 ?

VM dirty_ratio is set to 15 persistently?


---


## Q5: PASS

Weight: 6
Perform the following tasks:

Add a group called developers.

Add a user called jane. When creating the account, go with the system defaults. That is, the user's home directory should be set to /home/jane, and the default shell should be /bin/bash. You can pick any password you want for this user.

After the user jane was created, perform the following changes. Set her primary/login group to developers. And set her secondary groups to jane and sudo.


Is user jane created and added to required groups as per task?

User jane is using default shell?

---

## Q6: FAIL 

Weight: 4
Edit the configuration of the SSH daemon. Disable password logins globally (for all users). But make an exception for a single user. The user called "john" should be allowed to log in with a password.

Don't apply changes now.


Note: There's also a setting related to "Keyboard Interactive Authentication" that can in some cases affect password authentication settings. But you can ignore that setting for the purposes of this task.

PasswordAuthentication is set to no globally?

PasswordAuthentication for user John is set to yes?

### Solution
Edit the SSH daemon configuration file:

sudo vim /etc/ssh/sshd_config

Disable password authentication globally:

PasswordAuthentication no

Ensure the match block for user John is correct:
At the end of the file, you should have:

Match User john
    PasswordAuthentication yes

### Mistakes:
- Option kept at the end seems not recognized by KK

```text
# Example of overriding settings on a per-user basis
Match User john
#       X11Forwarding no
#       AllowTcpForwarding no
#       PermitTTY no
#       ForceCommand cvs server
        PasswordAuthentication yes
```


---

## Q7: PASS

Weight: 3
A large file in the /usr directory is no longer required. Find the file larger than 1 GB and delete it.


Identified the file and deleted it?

--- 

## Q8: PASS

Weight: 8
We've provided two storage devices that can be accessed at /dev/vdb and /dev/vdc. Add these as physical volumes to LVM, then set up a volume group that includes both devices. Call this volume group VG1 (uppercase letters). On this volume group, add a logical volume of exactly 6 GB. Call this logical volume LV1 (uppercase letters).


Is /dev/vdb, /dev/vdc added as physical volumes to the VG1 volume group ?

VG1 should be exactly this name (uppercase letters), and it shouldn't use <6 GB ?

Is logical volume should be called exactly LV1, created on VG1 with a size of 6 GB?



---

## Q9. FAIL

Weight: 10
Set up iptables rules that will redirect all incoming network connections from "10.11.12.0/24" to "10.9.9.1". Remember the matching masquerade rule (so traffic can be returned to 10.11.12.0/24).


Prerouting rules are set up as given in task?

Postrouting rules are set as given in task ?

### Solution
Setup Prerouting rule as below:

sudo iptables -t nat -A PREROUTING -s 10.11.12.0/24 -p tcp -j DNAT --to-destination 10.9.9.1

Setup Postrouting rule as below:

sudo iptables -t nat -A POSTROUTING -s 10.11.12.0/24 -j MASQUERADE

### Mistakes
- Missed -p tcp
- missed -s in masquerade


---

## Q10: FAIL

Weight: 13
Use the kode_web directory to build a Docker image. Fix any errors if necessary. Call your image kodekloud/nginx_kodekloud:1.0. Run a Docker container based on this image, call the container kodekloud_webserv and redirect any connections incoming to port 81 on the host to port 80 of the container.

Perform this task on node01. You can access node01 ssh node01 and password caleston123


Is docker image is created with name kodekloud/nginx_kodekloud ?

Is container kodekloud_webserv created?

Is the container started and ports redirected correctly ?


### Solution
Run below command

docker build --tag kodekloud/nginx_kodekloud:1.0 /home/bob/kode_web

you will see below error

   2 | >>> COPi index.html /usr/share/nginx/html/index.html

ERROR: failed to solve: dockerfile parse error on line 2: unknown instruction: COPi (did you mean copy?)

There is a typo in the Dockerfile, correct it From COPi to COPY.
Now build image:

docker build --tag kodekloud/nginx_kodekloud:1.0 /home/bob/kode_web

check the images created by command docker images

Spin up the container by the below command:

docker run --detach --publish 81:80 --name kodekloud_webserv kodekloud/nginx_kodekloud:1.0


### Mistakes
- Not practiced, didn't try

---

## Q11: PASS

Weight: 9
You will find a subdirectory called kode. This is a local Git repository. And it's already configured with a remote repository aliased as origin. Perform the following tasks:

Pull in the latest commits from this origin remote repository, from the master branch.

You should now see a file called file1. Edit this file and add another line to it. Write this text on the second line: "line2". Save the file.

Stage and commit your latest changes to file1. Add the following message to this commit: "Added line2 to our project".

Push your local Git repository to the remote repository aliased as "origin", into the "master" branch.


The changes are pushed to git repo.

---

## Q12: FAIL

Weight: 4
Format /dev/vdd with the ext4 filesystem. Then set up the system to automatically mount /dev/vdd into the /home/bob/backups directory every time it boots up.


Is /dev/vdd formatted with ext4 filesystem ?

Does the /home/bob/backups directory mounted to dev/vdd?


### Solution
Format /dev/vdd with ext4 Filesystem

sudo mkfs.ext4 /dev/vdd

Set up Automatic Mounting

sudo vim /etc/fstab

Add an entry for /dev/vdd:

/dev/vdd /home/bob/backups ext4 defaults 0 2

Test the /etc/fstab Entry:

sudo mount -a  

### Mistakes
- Lazy to whether /home/bob/backups exists or not. it doesn't exists rest of all done correct

---


## Q13: PASS

Weight: 7
A process (mockexam.sh) is overusing system resources on node01. Identify the process consuming the most system resources.

Kill the process
Delete the script which is the source for that process.

Note: Perform this task in node01. Access node by ssh node01 Passoword: caleston123

Process is killed?

Source script is deleted?

---

## Q14: PASS

Weight: 4
Find all files in the /opt/files/ directory that are larger than 500 megabytes and delete them.


Files identified and deleted?


---


## Q15: PASS

Weight: 3
A process is overusing CPU resources. Find the most CPU-intensive process and close it.


Is CPU-intensive process killed?

---

## Q16: FAIL

Weight: 3
This system currently has no way of synchronizing its time with NTP servers. Install the timesyncd utility belonging to the systemd suite of software. Then configure it to synchronize with the following NTP pools:

0.asia.pool.ntp.org
1.asia.pool.ntp.org
2.asia.pool.ntp.org
3.asia.pool.ntp.org

Make sure these settings are applied.


Required NTP pools are configured?


### Solution
Install timesyncd utility

sudo apt install systemd-timesyncd

Edit /etc/systemd/timesyncd.conf to configure pools mentioned in question.

sudo vim /etc/systemd/timesyncd.conf

Uncomment the NTP line and after adding pools the line should be as below

NTP=0.asia.pool.ntp.org 1.asia.pool.ntp.org 2.asia.pool.ntp.org 3.asia.pool.ntp.org

To apply the settings we should run below command

sudo systemctl restart systemd-timesyncd

### Mistakes:

- Not restarted the service

---

## Q17: PASS

Weight: 6
We have a Network Block Device (NBD) server running at the IP address 127.0.0.1. Perform the following tasks:

A network block device is already attached to this system and mounted to a certain directory. Unmount the network block device from that directory. Then disconnect the network block device from the system.

List the exports available on the NBD server at 127.0.0.1. Attach the second export to this system, and then mount it to the /mnt directory.


Note: Perform this task in node01. Access node by ssh node01 Passoword: caleston123

Is mnt directory is mounted to /dev/nbd0 ?

Is the size of /dev/nbd0 is 2G?


---


## Retro:

- More Silly Mistakes, not focused attentively.
