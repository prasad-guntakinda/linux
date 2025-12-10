# Practices



## Service Configuration — LFCS Practice Questions

Use these to test your knowledge. Try to answer the question first; open the collapsible answer/hints when ready.

### Question 1 — Unit Locations
Where does `systemd` look for unit files? Name the directories in precedence order and explain why you should not edit files in the vendor directory directly.

<details>
<summary>Answer / Hints</summary>

Precedence (higher → lower):

- `/etc/systemd/system` — admin/local units and drop-ins (highest precedence)
- `/run/systemd/system` — runtime/transient units
- `/lib/systemd/system` or `/usr/lib/systemd/system` — vendor/package-supplied units (lowest precedence)

Why not edit vendor files: package updates can overwrite vendor files; use drop-in overrides (`/etc/systemd/system/<unit>.d/override.conf`) or copy the unit to `/etc` if you need a full local override.

</details>

### Question 2 — Enabling at boot
Describe the commands to: start a service now, enable it to start at boot, and enable+start in a single command. Also show how to disable and stop it.

<details>
<summary>Answer / Hints</summary>

Commands:

- Start now: `sudo systemctl start <name>.service`
- Enable at boot: `sudo systemctl enable <name>.service`
- Enable and start now: `sudo systemctl enable --now <name>.service`
- Stop now: `sudo systemctl stop <name>.service`
- Disable at boot: `sudo systemctl disable <name>.service`

Check status with `sudo systemctl status <name>.service` and whether enabled with `systemctl is-enabled <name>.service`.

</details>

### Question 3 — Drop-in overrides
How do you make a small change (for example, add an environment variable or change `RestartSec=`) to a service without copying the entire unit file? Show the command you would use and where the change is stored.

<details>
<summary>Answer / Hints</summary>

Use `systemctl edit <unit>` which opens an editor to create a drop-in file under `/etc/systemd/system/<unit>.d/override.conf`.

Example content to set an environment variable:

```ini
[Service]
Environment=MYVAR=hello
RestartSec=10
```

Then run `sudo systemctl daemon-reload` (often `systemctl edit` triggers reload) and `sudo systemctl restart <unit>`.

</details>

### Question 4 — Reload vs Restart
Explain the difference between `systemctl reload <unit>` and `systemctl restart <unit>`. When would `reload-or-restart` be useful?

<details>
<summary>Answer / Hints</summary>

`reload` asks the service to re-read its configuration (typically via `SIGHUP` or a configured `ExecReload`) without stopping the process. `restart` stops and starts the service (brief downtime). `reload-or-restart` will send reload if supported, otherwise performs a restart — useful for applying config changes safely when the service supports graceful reload.

</details>

### Question 5 — Masking
What does `systemctl mask <unit>` do and how is it different from `disable`? When would you use `mask`?

<details>
<summary>Answer / Hints</summary>

`mask` creates a symlink from the unit to `/dev/null` which prevents the unit from being started (manually or as a dependency). `disable` simply prevents automatic start at boot but does not prevent manual `systemctl start`. Use `mask` to block a unit entirely (e.g., prevent accidental starts of a service with known issues).

</details>

### Question 6 — Dependency ordering
Given a service unit A that depends on service B being up before it starts, which unit settings express that dependency and in what way? Provide an example snippet.

<details>
<summary>Answer / Hints</summary>

Use `After=` to order start-up (A starts after B), and `Requires=` or `Wants=` to express dependency. `Requires=` makes the unit fail if the dependency fails to start; `Wants=` is a weaker dependency.

Example:

```ini
[Unit]
Description=Service A
After=network.target B.service
Requires=B.service
```

</details>

### Question 7 — Service types
Name at least three `Type=` values for service units (e.g., `simple`) and explain when to use each.

<details>
<summary>Answer / Hints</summary>

- `simple`: systemd starts the `ExecStart` process and assumes it is the main service process (default). Use for foreground processes.
- `forking`: for daemons that fork into background (SysV-style). Use if the process double-forks to background.
- `oneshot`: for one-time tasks (run and exit). Use for init-time tasks.
- `notify`: the service notifies systemd via sd_notify when ready — useful for services that need to do initialization.

</details>

### Question 8 — Logs and troubleshooting
How do you view the last 200 log lines for a service and follow new logs live? Also show a command to view logs only for the current boot.

<details>
<summary>Answer / Hints</summary>

View logs (last 200 lines): `sudo journalctl -u <unit> -n 200 --no-pager`
Follow live: `sudo journalctl -u <unit> -f`
Current boot only: `sudo journalctl -u <unit> -b`

</details>

### Question 9 — Resource limits via systemd
How would you limit a service to use at most 200MB RAM and 50% CPU quota using systemd unit settings? Where do you add these directives?

<details>
<summary>Answer / Hints</summary>

Add resource directives under the `[Service]` section of the unit or drop-in override, for example:

```ini
[Service]
MemoryMax=200M
CPUQuota=50%
```

Place in `/etc/systemd/system/<unit>.d/override.conf` and `systemctl daemon-reload` then restart the unit.

</details>

### Question 10 — Timers
Explain how to run a script every day at 2:30am using a `systemd` timer unit (briefly outline the `*.service` and `*.timer` files and the key fields).

<details>
<summary>Answer / Hints</summary>

Create a oneshot service `/etc/systemd/system/myscript.service` with `ExecStart=/path/to/script.sh`. Create `/etc/systemd/system/myscript.timer` with:

```ini
[Timer]
OnCalendar=*-*-* 02:30:00
Persistent=true

[Install]
WantedBy=timers.target
```

Enable and start the timer: `sudo systemctl enable --now myscript.timer`.

</details>

### Question 11 — Safe edits and daemon-reload
You edited a unit file under `/etc/systemd/system`. What command must you run to make systemd notice the changes? If the unit is already running, how do you apply the changes?

<details>
<summary>Answer / Hints</summary>

Run `sudo systemctl daemon-reload` to reload unit files. To apply changes to a running unit, run `sudo systemctl restart <unit>` (or `reload` if the service supports it).

</details>

### Question 12 — Exam-style practical
Write the minimal `ExecStart` and `[Install]` sections for a unit that runs `/usr/local/bin/myapp` as user `appuser` and should start at boot in multi-user.target.

<details>
<summary>Answer / Hints</summary>

Minimal example:

```ini
[Service]
User=appuser
ExecStart=/usr/local/bin/myapp
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Save as `/etc/systemd/system/myapp.service`, then `sudo systemctl daemon-reload` and `sudo systemctl enable --now myapp.service`.

</details>

## Hands-On Lab: Service Configuration Exercises

Run this lab in a disposable VM. Each step includes the command to run and the expected outcome.

1) Create a simple demo script

```bash
# as root or with sudo
sudo tee /usr/local/bin/demoapp.sh > /dev/null <<'EOF'
#!/bin/bash
echo "demoapp: started at $(date)" | systemd-cat -t demoapp -p info
sleep 60
echo "demoapp: exiting at $(date)" | systemd-cat -t demoapp -p info
EOF
sudo chmod +x /usr/local/bin/demoapp.sh
```

Expected: the script is executable at `/usr/local/bin/demoapp.sh` and will log messages to the journal when run.

2) Create a systemd service unit

```bash
sudo tee /etc/systemd/system/demoapp.service > /dev/null <<'EOF'
[Unit]
Description=Demo App (LFCS lab)
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/demoapp.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable --now demoapp.service
```

Expected: `demoapp.service` is enabled and started. `systemctl status demoapp.service` shows `active (running)` or briefly `active (exited)` depending on timing; logs appear in the journal.

3) Inspect logs and status

```bash
sudo systemctl status demoapp.service --no-pager
sudo journalctl -t demoapp -n 200 --no-pager
sudo journalctl -u demoapp.service -n 50 --no-pager
```

Expected: you see the info messages logged by the script and the service status lines.

4) Make a drop-in override (add an environment variable and change restart delay)

```bash
sudo systemctl edit demoapp.service
# in the editor add:
# [Service]
# Environment=DEMO_MODE=testing
# RestartSec=2
# save and exit
sudo systemctl daemon-reload
sudo systemctl restart demoapp.service
```

Expected: override is created at `/etc/systemd/system/demoapp.service.d/override.conf`. Restart uses the new `RestartSec` and the environment will be available to the process (viewable via `/proc/<pid>/environ` for the running PID).

5) Limit resources for the service (test resource directives)

```bash
sudo mkdir -p /etc/systemd/system/demoapp.service.d
sudo tee /etc/systemd/system/demoapp.service.d/resource.conf > /dev/null <<'EOF'
[Service]
MemoryMax=50M
CPUQuota=20%
EOF
sudo systemctl daemon-reload
sudo systemctl restart demoapp.service
systemctl show demoapp.service -p MemoryCurrent -p CPUAccounting --no-pager
```

Expected: systemd enforces the resource directives (MemoryMax/CPUQuota). Use `systemctl show` and `journalctl` to confirm no errors on start.

6) Create a timer to run the demo script in future (test with OnCalendar = +1min)

```bash
sudo tee /etc/systemd/system/demoapp-run.service > /dev/null <<'EOF'
[Unit]
Description=Run demoapp once (timer)

[Service]
Type=oneshot
ExecStart=/usr/local/bin/demoapp.sh
EOF

sudo tee /etc/systemd/system/demoapp-run.timer > /dev/null <<'EOF'
[Unit]
Description=Timer to run demoapp in 1 minute

[Timer]
OnActiveSec=60
Persistent=false

[Install]
WantedBy=timers.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable --now demoapp-run.timer
systemctl list-timers --all | grep demoapp
```

Expected: timer is listed and will trigger `demoapp-run.service` roughly after 1 minute; check journal for the run.

7) Cleanup (remove units and script)

```bash
sudo systemctl disable --now demoapp.service demoapp-run.timer demoapp-run.service || true
sudo rm -f /etc/systemd/system/demoapp* /usr/local/bin/demoapp.sh
sudo systemctl daemon-reload
sudo systemctl reset-failed
```

Safety notes:
- Run this lab in a VM or disposable environment. Do not run destructive commands on production systems.
- The script sleeps for 60s — adjust durations if you prefer faster tests.

---


## LFCS Practice: Diagnose & Manage Processes

Practice diagnosing and managing processes. Attempt each question, then open the answer to check commands and hints.

### Question 1 — Interpret `ps` output
You run: `ps -eo pid,ppid,cmd,%cpu,%mem,etimes --sort=-%cpu | head -n 12`. The top line shows a long-running process with PID 2345, %CPU 95.0, etimes 3600. What do these fields mean and what is your immediate next diagnostic command to learn more about the process?

<details>
<summary>Answer / Hints</summary>

- `pid`: process id; `ppid`: parent pid; `cmd`: command line; `%cpu`: CPU percentage; `%mem`: memory percent; `etimes`: elapsed time (seconds). 95% CPU and 3600 seconds indicates a runaway process.
- Next: inspect command line and open files: `ps -p 2345 -o pid,ppid,user,cmd,etime` and `ls -l /proc/2345/exe` and `sudo lsof -p 2345`.

</details>

### Question 2 — Find processes by name and kill gracefully
How do you find all processes named `myworker` and send them SIGTERM; if they don't exit in 10s, send SIGKILL?

<details>
<summary>Answer / Hints</summary>

Use `pgrep` and `pkill` or scripting:

```bash
pgrep -a myworker
pkill -TERM myworker
sleep 10
pkill -KILL myworker || true
```

Or per-PID:

```bash
for pid in $(pgrep myworker); do sudo kill -TERM "$pid"; done
sleep 10
for pid in $(pgrep myworker); do sudo kill -KILL "$pid"; done
```

</details>

### Question 3 — Which process holds a TCP port?
Port 8080 is busy. Provide a command to identify the PID and the command that owns the socket.

<details>
<summary>Answer / Hints</summary>

Use `ss` or `lsof`:

```bash
sudo ss -ltnp '( sport = :8080 )'
# or
sudo lsof -iTCP:8080 -sTCP:LISTEN -Pn
```

This shows the PID and program name for the listening socket.

</details>

### Question 4 — Use `strace` safely
You suspect a process is stuck in a syscall. How do you attach `strace` to an existing PID 3456 to log file operations only, and how do you detach without killing the process?

<details>
<summary>Answer / Hints</summary>

Attach for file-related syscalls and write to a file:

```bash
sudo strace -f -e trace=file -o /tmp/strace-3456.log -p 3456
```

Detach by pressing Ctrl-C in the strace terminal (it stops tracing but does not kill the traced process). Alternatively use `-D` or run from another VT and kill the strace process (`sudo pkill -P <strace-pid>`).

</details>

### Question 5 — Inspect open file descriptors
How can you list files and sockets a process 4567 has open, and how do you check which file is its current working directory?

<details>
<summary>Answer / Hints</summary>

List open fds:

```bash
ls -l /proc/4567/fd
sudo lsof -p 4567
```

Check current working directory:

```bash
readlink -f /proc/4567/cwd
```

</details>

### Question 6 — Background jobs and shell job control
A user started a long job in an interactive shell and pressed Ctrl-Z. How do they resume it in background and later bring it back to foreground?

<details>
<summary>Answer / Hints</summary>

List jobs:

```bash
jobs -l
# resume in background
bg %1
# bring to foreground
fg %1
```

`%1` is the job id shown by `jobs`.

</details>

### Question 7 — Adjusting priority with `nice` / `renice`
How do you start a program `heavy.sh` with lower priority, and later reduce priority of an existing process 5678?

<details>
<summary>Answer / Hints</summary>

Start with niceness +10 (lower CPU priority):

```bash
nice -n 10 /path/to/heavy.sh &
```

Change existing PID niceness to +15:

```bash
sudo renice +15 -p 5678
```

Lower nice value (higher priority) requires privileges.

</details>

### Question 8 — Identify zombie processes
How do you find zombie processes and what is the typical remedy to clear them?

<details>
<summary>Answer / Hints</summary>

Find zombies:

```bash
ps -eo pid,ppid,stat,cmd | awk '$3 ~ /Z/ {print}'
# or
ps aux | awk '$8=="Z" {print}'
```

Remedy: identify parent PID (PPID). Either restart the parent process gracefully or, if possible, have the parent `wait()` for children. If parent is defunct and cannot be fixed, restart the parent or, as last resort, reboot.

</details>

### Question 9 — Trace which files a process reads at startup
You want to know what configuration files a service read during startup. Which tool and strategy do you use?

<details>
<summary>Answer / Hints</summary>

Use `strace -f -e trace=file -o /tmp/startup.log <command>` when starting the service in a container or test environment. For systemd services, run the unit's `ExecStart` command manually under `strace` or enable `strace` via an ExecStart wrapper; avoid attaching to a running process if you need open-at-start behavior. Then inspect `/tmp/startup.log` for `open`/`access` syscalls.

</details>

### Question 10 — From service to PID
Given a systemd unit `nginx.service`, how do you find the main PID that systemd tracks for the service and the command line it was started with?

<details>
<summary>Answer / Hints</summary>

Use `systemctl show` and `/proc`:

```bash
systemctl show -p MainPID nginx.service
PID=$(systemctl show -p MainPID --value nginx.service)
ps -p $PID -o pid,cmd
readlink -f /proc/$PID/exe
cat /proc/$PID/cmdline | tr '\0' ' '\n
```

If `MainPID=0` the service may have exited or uses Type=oneshot/notify semantics.

</details>

---


## Package Manager: (RHEL-Based Systems Only)


Using **DNF/YUM** (Rocky, AlmaLinux, CentOS Stream, RHEL)

---

## 🔍 1. Search for Packages

### Search by package name

```bash
# Search by package name
dnf search httpd
# Search by description/keywords
dnf search firewall
# Show detailed package information
dnf info httpd
```

---

## 📦 2. Install Packages

### Install a package

```bash
dnf install httpd -y
dnf install vim-enhanced -y
dnf install mariadb-server -y
# Install a specific version
dnf install nginx-1:1.20.1-14.el9

# Update all system packages
dnf update -y
```

---

## 🧪 4. Validate Installed Packages


```bash
# Check if a package is installed
dnf list installed | grep httpd
# List all files provided by a package
rpm -ql httpd
# Package integrity check
rpm -V httpd
```

---

## 🗑️ 5. Remove Packages


```bash
# Remove a package
dnf remove httpd -y
```

---

## 📚 6. Manage Repositories

```bash
# List enabled repositories
dnf repolist
# List all repositories (enabled + disabled)
dnf repolist all
# Enable a repository
dnf config-manager --set-enabled extras
# Disable a repository
dnf config-manager --set-disabled epel
```

### Install EPEL repository

```bash
dnf install epel-release -y
```

### Add a custom repository

Create file:

```
/etc/yum.repos.d/custom.repo
```

Add:

```ini
[customrepo]
name=My Custom Repo
baseurl=http://repo.example.com/rpm
enabled=1
gpgcheck=0
```

---

## 🔧 7. Manage Services After Package Installation

```bash
# Enable & start a service
systemctl enable --now httpd
# Check service status
systemctl status httpd
```

---

## ♻️ 8. Clean Package Cache

### Clean all cached data

```bash
dnf clean all
```

---

## Change Kernel Runtime Parameters:

- Non-persistent (runtime only) using `sysctl`
- Persistent (survive reboot) using `/etc/sysctl.conf` or `/etc/sysctl.d/*.conf`







