# systemd Cheat Sheet

A compact, practical guide to `systemd` for LFCS preparation: concepts, common commands, unit file examples, timers, logging and troubleshooting.

## Quick definition
- `systemd` is the init system and service manager used by most modern Linux distributions. It runs as PID 1, manages units (services, sockets, mounts, timers, targets), provides logging via `journalctl`, and integrates with cgroups for process management.

## Key concepts
- Units: configuration objects. Common types: `service`, `socket`, `timer`, `mount`, `target`.
- Unit files: usually in `/lib/systemd/system/` (package) or `/etc/systemd/system/` (admin overrides).
- Targets: group units together (like old SysV runlevels). Examples: `multi-user.target`, `graphical.target`.
- `systemctl`: primary CLI to interact with `systemd`.
- `journalctl`: reads the binary journal (logs).

## Useful `systemctl` commands
```bash
# Show status of a service
sudo systemctl status nginx.service

# Start / stop / restart
sudo systemctl start myapp.service
sudo systemctl stop myapp.service
sudo systemctl restart myapp.service

# Enable at boot / disable
sudo systemctl enable myapp.service
sudo systemctl disable myapp.service

# Enable and start now
sudo systemctl enable --now myapp.service

# List units (services) and failed units
systemctl list-units --type=service
systemctl --failed

# Reload systemd to pick up changed unit files
sudo systemctl daemon-reload

# Mask (block) and unmask a service
sudo systemctl mask some.service
sudo systemctl unmask some.service

# Switch to a different target (like runlevel)
sudo systemctl isolate multi-user.target

# Power operations (works via systemd)
sudo systemctl reboot
sudo systemctl poweroff
sudo systemctl halt
```

## Logging with `journalctl`
```bash
# Show logs for a unit
journalctl -u myapp.service

# Tail logs
journalctl -u myapp.service -f

# Show journal since boot
journalctl -b

# Show journal for current boot with human timestamps
journalctl -b --no-pager --output=short-iso

# Limit by time
journalctl --since "2025-11-29 10:00" --until "2025-11-29 12:00"
```

## Unit file: minimal `service` example
Create `/etc/systemd/system/myapp.service`:

```ini
[Unit]
Description=MyApp service
After=network.target

[Service]
Type=simple
User=someuser
WorkingDirectory=/home/someuser/myapp
ExecStart=/usr/bin/env python3 /home/someuser/myapp/app.py
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
```

Commands to enable and run it:
```bash
sudo systemctl daemon-reload
sudo systemctl enable --now myapp.service
sudo journalctl -u myapp.service -f
```

Notes:
- `Type=`: `simple` (default), `forking` (SysV-style daemons), `oneshot` (one-off tasks), `notify`, `idle`.
- `Restart=` helps keep services running (`on-failure`, `always`, `on-abnormal`).

## Timer unit example (replace cron)
Create `/etc/systemd/system/mytask.timer` and `/etc/systemd/system/mytask.service`.

`/etc/systemd/system/mytask.service`:
```ini
[Unit]
Description=My periodic task

[Service]
Type=oneshot
ExecStart=/usr/local/bin/mytask.sh
```

`/etc/systemd/system/mytask.timer`:
```ini
[Unit]
Description=Run mytask every 15 minutes

[Timer]
OnCalendar=*:0/15
Persistent=true

[Install]
WantedBy=timers.target
```

Enable and start the timer:
```bash
sudo systemctl daemon-reload
sudo systemctl enable --now mytask.timer
systemctl list-timers --all
```

## Socket activation (on-demand service start)
- Create a `.socket` unit that listens on a socket. systemd starts the associated `.service` on first connection.
- Example: `ssh.socket` can activate `sshd.service` on demand.

## Resource control (cgroups) options
- systemd integrates cgroups; set limits in unit files:
```ini
[Service]
MemoryMax=200M
CPUQuota=50%
```

## Security-related unit options (short list)
- `PrivateTmp=yes` — private `/tmp` namespace.
- `NoNewPrivileges=yes` — prevents privilege escalation.
- `ProtectSystem=full` — mount `/usr` and `/boot` read-only for the unit.
- `ReadOnlyPaths=` / `InaccessiblePaths=` — restrict file system access.

## Troubleshooting
- `systemctl status` shows unit logs and last exit codes.
- `journalctl -xe` for recent priority messages and errors.
- If unit changes don't take effect: `sudo systemctl daemon-reload` then restart the unit.
- Check permissions: ensure `ExecStart` binary is executable and the `User=` exists.

## Common LFCS-style tasks (commands summary)
| Task                             | Command                                |
| -------------------------------- | -------------------------------------- |
| Start service now                | `sudo systemctl start <name>.service`  |
| Enable service at boot           | `sudo systemctl enable <name>.service` |
| Check service status             | `sudo systemctl status <name>.service` |
| View logs                        | `journalctl -u <name>.service`         |
| List failed units                | `systemctl --failed`                   |
| Reload units after editing files | `sudo systemctl daemon-reload`         |

## Example: small Python app service (complete walkthrough)
1. Create a simple app at `/home/you/simple_app/app.py`:
```python
#!/usr/bin/env python3
import time
if __name__ == '__main__':
    while True:
        print('simple_app running')
        time.sleep(10)
```
2. Make it executable:
```bash
chmod +x /home/you/simple_app/app.py
```
3. Create unit `/etc/systemd/system/simple_app.service`:
```ini
[Unit]
Description=Simple Python App

[Service]
Type=simple
ExecStart=/home/you/simple_app/app.py
Restart=always
User=you

[Install]
WantedBy=multi-user.target
```
4. Enable and start:
```bash
sudo systemctl daemon-reload
sudo systemctl enable --now simple_app.service
sudo journalctl -u simple_app.service -f
```

## References
- `man systemd`, `man systemctl`, `man journalctl`, `man systemd.unit`
- systemd documentation: https://www.freedesktop.org/wiki/Software/systemd/

---
This cheat sheet is designed to be copy-paste ready; adjust paths and usernames before running commands on production systems.
