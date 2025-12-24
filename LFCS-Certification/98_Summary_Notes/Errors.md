

# nginx:

The error nginx: [emerg] socket() [::]:80 failed (97: Address family not supported by protocol) occurs because Nginx is configured to listen for IPv6 traffic (represented by [::]), but IPv6 is disabled on your server's network stack or kernel. 
To fix this, you have two primary options: disable IPv6 in the Nginx configuration (recommended for local/internal environments) or enable IPv6 on the system.
Option 1: Disable IPv6 in Nginx (Quickest Fix)
Since Nginx cannot bind to an IPv6 socket that doesn't exist, you must comment out or remove the IPv6 listen directives. 
Edit your site's configuration file. This is usually the default file:
sudo nano /etc/nginx/sites-available/default
Find the IPv6 listen line. Look for a line that contains [::]:80.
Locate: listen [::]:80 default_server;
Comment it out by adding a # at the beginning:
# listen [::]:80 default_server;
Save and exit (Ctrl+O, Enter, Ctrl+X).
Test the configuration:
sudo nginx -t
Restart Nginx:
sudo systemctl restart nginx 
Option 2: Enable IPv6 on the System
If you actually need IPv6 support, you must ensure it is not disabled in your system's configuration. 
Check sysctl settings: Open the configuration file:
sudo nano /etc/sysctl.conf
Ensure these values are set to 0 (or removed if they are already 1):
ini
net.ipv6.conf.all.disable_ipv6 = 0
net.ipv6.conf.default.disable_ipv6 = 0
Use code with caution.

Apply changes:
sudo sysctl -p
Check GRUB (Advanced): If the above doesn't work, ensure ipv6.disable=1 is not in your boot parameters in /etc/default/grub. If it is, remove it and run sudo update-grub. 
Why did this happen?
Many cloud providers or custom kernel configurations disable IPv6 to reduce complexity or for security. However, Nginx's default installation on Ubuntu 24.x often includes a listen [::]:80 directive by default, leading to this mismatch on boot


