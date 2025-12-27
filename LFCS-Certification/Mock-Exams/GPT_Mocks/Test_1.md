# Test_1
This mock exam is designed to simulate the 2025 LFCS (Linux Foundation Certified System Administrator) environment. It covers a broad range of domains including Storage, Networking, Services, and Security.

## Exam Guidelines
Time Limit: 2 hours.
Platform: Ubuntu 24.04/25.x (or compatible).
Success Criteria: All changes must be persistent (survive a reboot).
Access: Use sudo for administrative tasks.

# Domain 1: Storage Management (25%)
### Task 1.1: Logical Volume Management
    - Create a Physical Volume on /dev/sdb.
    - Create a Volume Group named vg_finance.
    - Create a Logical Volume named lv_reports with a size of 2GB.
    - Format the volume with XFS and mount it persistently at /mnt/finance.
## Task 1.2: Storage Expansion
    - Extend the lv_reports volume by an additional 1GB (total 3GB) without losing data.
    - Ensure the filesystem reflects the new size.
    - Task 1.3: Swap Space
    - Create a 512MB swap file at /extraswap.
    - Ensure it is activated automatically at boot with the correct permissions (0600).

# Domain 2: Networking & Services (25%)
Task 2.1: Static IP Configuration
Configure the primary interface (e.g., eth0 or enp0s3) with a static IP: 192.168.56.100/24.
Set the Gateway to 192.168.56.1 and DNS to 8.8.8.8.
Ensure the hostname is set to node-server.example.com.
Task 2.2: SSH Hardening
Configure the SSH daemon to:
Listen on port 2222.
Disable root login.
Allow only the user prasad to connect.
Task 2.3: NTP Synchronization
Configure the system to synchronize time with the server pool.ntp.org.
Ensure the timezone is set to UTC.
Domain 3: Identity & Security (20%)
Task 3.1: ACL Permissions
Create a file at /opt/shared_data.txt.
Grant the user alice read and write permissions using ACLs.
Ensure that no other users (besides owner and root) have any access.
Task 3.2: Sudo Access
Create a user named manager.
Configure sudo so that manager can run the /usr/bin/apt command without being prompted for a password.
Task 3.3: Firewall Rules (UFW)
Enable the UFW firewall.
Allow incoming traffic on port 2222/tcp (SSH) and port 80/tcp (HTTP).
Deny all traffic from the IP 192.168.1.50.
Domain 4: Virtualization & Containers (15%)
Task 4.1: Container Management
Install the docker.io engine.
Run a detached container named web-server using the nginx:alpine image.
Map the host port 8080 to the container port 80.
Task 4.2: Virtual Machines (libvirt)
List all virtual machines (including inactive ones) and save the output to /tmp/vms.txt.
Ensure the default virtual network is set to start automatically at boot.
Domain 5: Automation & Scripting (15%)
Task 5.1: Cron Scheduling
Schedule a job for the user root that appends the current date and time to /var/log/check-time.log every Monday through Friday at 3:15 AM.
Task 5.2: Log Analysis
Search the journal logs for all entries with a priority of "Error" or higher since the last boot.
Save these entries to /root/error_report.txt.
Task 5.3: Archive & Compress
Create a compressed tar archive of the /etc/ssh/ directory.
Save it as /root/ssh_backup.tar.gz.
Answer Key / Validation Commands
Task	Validation Command
1.1	lvs and findmnt /mnt/finance
1.2	vgs (check VFree) and df -h /mnt/finance
1.3	swapon --show
2.1	ip addr and hostnamectl
2.2	ss -tulpn | grep 2222 and grep "PermitRootLogin" /etc/ssh/sshd_config
3.1	getfacl /opt/shared_data.txt
3.2	sudo -u manager -l
3.3	sudo ufw status verbose
4.1	docker ps and curl -I localhost:8080
5.1	crontab -l
5.2	journalctl -p err -b
LFCS Exam Pro-Tips for 2025
Always use systemctl enable: Any service you start must also be enabled to persist after a reboot.
Test your /etc/fstab: After editing, run mount -a. If it fails, your system won't reboot.
Check man pages: You have access to them during the exam. Use man -k <keyword> if you forget a command.
YAML Syntax: Be extremely careful with spaces in Netplan (/etc/netplan/) files; one tab will break your network.