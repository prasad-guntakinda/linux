# Operations Deployment Summary Notes


        1. Boot, reboot, shutdown system safely
        2. Boot or change system into different operating modes
        3. Shell scripting to automate tasks
        4. Manage Startup Processes and Services
        5. Create systemd services
        6. Diagnose and Manage Processes
        7. Locate and Analyse System log files
        8. Schedule Tasks
        9. Manage software with Package Manager
        10. Configure Repositories for Package Manager
        11. Install software by compiling source code
        12. Verify Integrity and Availability of resources
        13. Change Kernel runtime parameters, Persistent and Non-persistent 
        14. List and Identify SELinux file and process context
        15. Create and enforce MAC using SELinux
        16. Create and Manage Containers
        17. Manage and Configure VMs
        18. Create and Boot a VM
        19. Installing an OS on VM

---

# 1. Boot, Reboot and Shutdown/Poweroff:

```bash
systemctl reboot
systemctl reboot --force
#to shutdown use poweroff keyword
systemctl poweroff
systemctl poweroff --force 
```

## Using cmd: shutdown

- The shutdown command is better suited for **scheduled reboots or shutdowns.**
- syntax: `shutdown [OPTIONS] [TIME] [MESSAGE]`
- Common Time Formats
    - The `[TIME]` argument is mandatory on many systems (like Ubuntu). 
    - `now`: Shutdown immediately.
    - `+m`: Shutdown in m minutes (e.g., +10).
    - `hh:mm`: Shutdown at a specific 24-hour time (anything between 00:00 and 23:59). 
- If you want to shutdown `x` minutes later, use `+x` instead.

````bash
# syntax
# shutdown [OPTIONS] [TIME] [MESSAGE]
# To shutdown at 2AM
sudo shutdown 02:00
# To shutdown after 15 minutes
sudo shutdown +15
# to reboot at 2AM
sudo shutdown -r 02:00
sudo shutdown -r +15
````

__wall message:__
- The "wall" message is the broadcast notification sent to all logged-in users and terminals when the system is being shut down, rebooted
- The shutdown utility publishes this message (internally using wall or equivalent) so users get a visible warning like: when shutdown will happen, who scheduled it, and any freeform text you provide.

````bash
sudo shutdown -r +10 'The system is going down for reboot in 10 minutes!'
sudo shutdown -r +5 "Rebooting for kernel update; save your work"
#halt immediately (poweroff depending on system).
sudo shutdown -h now "System maintenance: shutting down now"
#Cancel a scheduled shutdown Command:
sudo shutdown -c
# send warnings only
sudo shutdown -k +10 "Maintenance starts in 10 minutes (test only)"
# using wall command. warning only
echo "Heads up: quick maintenance in 15 minutes" | sudo wall
````
---

# 2. boot system into different operation modes

````bash
systemctl get-default
#graphical.target

#change default target
sudo systemctl set-default multi-user.target
# you can change it to emergency.target or rescue.target
# requires reboot to effect
# to change without reboot 
sudo systemctl isolate graphical.target

````

 systemd targets: 

| Target                | Description                                                                     | Use Case                                                           |
| --------------------- | ------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| ``graphical.target``  | Boots into a full graphical desktop environment.                                | Standard desktop usage.                                            |
| ``multi-user.target`` | Boots into a text-based environment with network services, but no GUI.          | Servers or other systems that don't require a graphical interface. |
| ``rescue.target``     | Loads a minimal set of services with a root shell.                              | For administrative tasks and maintenance in a minimal environment. |
| ``emergency.target``  | Boots with only the most basic system services and a read-only root filesystem. | Critical troubleshooting when standard and rescue modes fail.      |


---


# 3. Scripting to Automate the Tasks

- __Scripts:__ Scripts are simply files where we can add multiple instructions for our command interpreter. The interpreter can then read this file and execute instructions in order. First, it will execute the instruction on the first line, then the one on the second line, and so on.

- __#!/bin/bash__ 
- To make our script work, this has to be on the first line, not the second or third. Also, make sure there is no space before ``#``.
- ``#!`` is called a **shebang**. What follows ``#!`` is the full path to the command interpreter that we want to run this script. In this case we choose ``/bin/bash``.

- `#!/usr/bin/python3`: scripts will be exceuted by python3 interpreter.

- create `test.sh`

```bash
#!/bin/bash
export TEST_NAME=Prasad
echo "Welcome to Shell Scripting.....$TEST_NAME"
```
- execute: `chmod u+x test.sh` and ` ./test.sh`

## Bash Built-ins:

- To write advanced scripts with conditions, loops and so on we use Bash built-ins

- These are the built-in commands are defined internally to write shell scripts
- use `help` command to display all the built-in commands
- Type `help name` to find out more about the function name'.

````bash
# list all bash built-ins
help
# see help for if built-in usage
help if
# see help for kill built-in usage
help kill

help test
````

- Now lets create a log file if it doesn't exit oterwise take a backup and crete a new log file
- create ``test-if.sh`` file
````bash
#!/bin/bash

if test -f /tmp/practices/script.log; then
    mv /tmp/practices/script.log /tmp/practices/script.log.backup
    touch /tmp/practices/script.log
else
    touch /tmp/practices/script.log
fi

````

- It's important to note that almost any command can be used in an if block. That's because most commands return a so-called __"exit status code"__. 
- They return ``0`` if the command executed successfully, otherwise ``larger than 0`` if the command didn't find what it was looking for, or if it encountered an error.
- When "if" sees that the command returned `0`, it considers that this returned `TRUE`. When the command returns a non-zero value, "if" considers that the command returned `FALSE`.
- For example, consider the grep command. If it finds the text you were looking for, it returns 0. If it doesn't, it returns 1.

---

# 4. Manage Startup Processes and Services:


## systemd

- __Definition:__ systemd is an init system and system manager for Linux — the first process started by the kernel (PID 1) that initializes the user space and manages services, devices and the overall boot/shutdown lifecycle.

- systemd is the name of a large collection of tools and components, but also the name of the application responsible for system initalization and monitoring the system as a whole to ensure smooth operation.

- A Unit is any resource that systemd knows how to manage.
- Unit files live under ``/etc/systemd/system/`` and ``/lib/systemd/system/``.

- Systemd categorizes these by their file extensions. 
- Common types include:
    - `.service`: A program (like a web server).
    - `.mount`: A hard drive or partition.
    - `.timer`: A scheduled task (like a digital alarm clock).
    - `.target`: A group of units 

- A __Service__ is the most common type of unit. It represents a background process (daemon) that provides a specific function to the system.

- Simple Example: The Apache Web Server
- The service file is usually located at `/lib/systemd/system/apache2.service`.
- It contains instructions like:
    - What to run: `ExecStart=/usr/sbin/apache2`
    - When to run: "Only after the network is online."
    - What to do if it crashes: `Restart=always`

- Examples:

````bash

sudo systemctl start ssh.service
sudo systemctl stop/status/restart/reload ssh.service
# reloads the configuration without stopping it
sudo systemctl reload ssh.service
# first it reloads config if it needs restart
sudo systemctl reload-or-restart ssh.service
# enable to start the app at bootup time
sudo systemctl enable ssh.service
sudo systemctl status/is-enabled ssh.service
#disable to start at bootup
sudo systemctl disable ssh.service
# to enable and start the service
sudo systemctl enable --now ssh.service
# get a list of all service units available on the system, no matter if they're currently enabled, disabled, started or stopped

systemctl list-units --type service –all
````
---

## Create systemd services
