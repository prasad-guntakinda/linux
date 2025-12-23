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



