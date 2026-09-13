# Useful Linux / Server Commands

A practical command reference used while administering and troubleshooting the server.

The list focuses on commands that are frequently useful in this project, as well as commands that are easy to forget but important for system administration.

---

## System information

### `hostnamectl`

Show or change the system hostname and basic OS information.

```bash
hostnamectl
```

Useful for quickly checking hostname, OS, kernel and architecture.

### `uname -a`

Show kernel and system information.

```bash
uname -a
```

### `lsb_release -a`

Show Linux distribution information.

```bash
lsb_release -a
```

### `uptime`

Show how long the system has been running and current load.

```bash
uptime
```

---

## Systemd / Services

### `systemctl status <service>`

Check the current state of a service.

```bash
systemctl status ssh
```

### `systemctl start|stop|restart <service>`

Control a service.

```bash
sudo systemctl restart ssh
```

### `systemctl enable|disable <service>`

Configure whether a service starts automatically with the system.

```bash
sudo systemctl enable ssh
```

Important distinction:

- `start` → starts it now
- `enable` → starts it automatically on boot

### `systemctl --failed`

Show failed systemd units.

```bash
systemctl --failed
```

Useful for a quick system health check.

### `systemctl is-active <service>`

Return whether a service is currently active.

```bash
systemctl is-active ssh
```

### `systemctl is-enabled <service>`

Check whether a service is enabled at boot.

```bash
systemctl is-enabled ssh
```

---

## Logs

### `journalctl`

Read logs collected by systemd.

```bash
journalctl
```

### `journalctl -u <service>`

Show logs for a specific service.

```bash
journalctl -u ssh
```

### `journalctl -u <service> -f`

Follow new log entries in real time.

```bash
journalctl -u ssh -f
```

Very useful when restarting a service and watching what happens.

### `journalctl -b`

Show logs from the current boot.

```bash
journalctl -b
```

### `journalctl -b -1`

Show logs from the previous boot.

```bash
journalctl -b -1
```

This one is worth remembering when troubleshooting something that happened before a reboot.

---

## Networking

### `ip -br link`

Show network interfaces in a compact format.

```bash
ip -br link
```

Useful for quickly checking whether an interface is `UP`, `DOWN`, or has `NO-CARRIER`.

### `ip -br addr`

Show interfaces and assigned IP addresses.

```bash
ip -br addr
```

### `ip addr`

Detailed information about network interfaces and addresses.

```bash
ip addr
```

### `ip route`

Show the routing table.

```bash
ip route
```

Especially useful for checking the default route.

### `ip neigh`

Show the ARP/neighbor table.

```bash
ip neigh
```

Useful when troubleshooting local network connectivity.

---

## NetworkManager

### `nmcli device status`

Show network devices and their current state.

```bash
nmcli device status
```

### `nmcli connection show`

List configured NetworkManager connection profiles.

```bash
nmcli connection show
```

Important distinction:

- `device` → physical/logical network interface
- `connection` → configuration profile used by NetworkManager

### `nmcli device show <interface>`

Show detailed information about an interface.

```bash
nmcli device show enp4s0
```

Useful for checking IP configuration, gateway, DNS, carrier state and other properties.

### `nmcli connection show "<connection>"`

Show the configuration of a specific connection profile.

```bash
nmcli connection show "Ethernet connection 1"
```

Useful when an interface behaves differently from what you expect.

---

## Ethernet diagnostics

### `ethtool <interface>`

Show detailed Ethernet information.

```bash
sudo ethtool enp2s0
```

Useful for checking:

- link state
- negotiated speed
- duplex
- autonegotiation
- supported link modes

### `ethtool -i <interface>`

Show the driver and hardware information.

```bash
sudo ethtool -i enp2s0
```

Useful when investigating driver-related network problems.

### `ip link set <interface> up`

Bring an interface up manually.

```bash
sudo ip link set enp2s0 up
```

Use carefully on a remote server. Bringing an interface up is not the same as configuring its IP/routing.

---

## Netplan

### `netplan get`

Display the effective Netplan configuration.

```bash
sudo netplan get
```

### `netplan generate`

Generate backend configuration from Netplan YAML without applying it.

```bash
sudo netplan generate
```

Useful for checking whether configuration is syntactically valid.

### `netplan try`

Temporarily apply a Netplan configuration and automatically roll it back if it is not confirmed.

```bash
sudo netplan try
```

Particularly useful on remote servers because it provides a recovery mechanism if networking breaks.

### `netplan apply`

Apply Netplan configuration.

```bash
sudo netplan apply
```

Use carefully on a remote server because incorrect network configuration can terminate the SSH session.

---

## SSH

### `ssh <user>@<host>`

Connect to a remote server.

```bash
ssh denis@<PUBLIC_IP>
```

### `ssh -i <key> <user>@<host>`

Connect using a specific private key.

```bash
ssh -i ~/.ssh/id_ed25519 denis@<PUBLIC_IP>
```

### `ssh -o PubkeyAuthentication=no <user>@<host>`

Temporarily disable public-key authentication for a connection.

```bash
ssh -o PubkeyAuthentication=no denis@<PUBLIC_IP>
```

Useful for testing whether password authentication is actually disabled.

### `sshd -t`

Validate the SSH daemon configuration.

```bash
sudo sshd -t
```

No output normally means the configuration is syntactically valid.

### `sshd -T`

Show the effective SSH configuration after all configuration files have been processed.

```bash
sudo sshd -T
```

Very useful when multiple files under `/etc/ssh/sshd_config.d/` may override each other.

### `ss -tulpen`

Show listening TCP/UDP sockets and the processes using them.

```bash
sudo ss -tulpen
```

One of the most useful commands for checking what services are exposed by the server.

---

## Firewall — UFW

### `ufw status`

Show whether UFW is active.

```bash
sudo ufw status
```

### `ufw status verbose`

Show firewall status and default policies.

```bash
sudo ufw status verbose
```

### `ufw status numbered`

Show firewall rules with rule numbers.

```bash
sudo ufw status numbered
```

Useful when deleting or modifying a specific rule.

### `ufw allow <port>/tcp`

Allow incoming TCP traffic on a port.

```bash
sudo ufw allow 22/tcp
```

### `ufw delete <rule>`

Remove a firewall rule.

```bash
sudo ufw delete 2
```

Check `ufw status numbered` first so you do not delete the wrong rule.

### `ufw enable`

Enable the firewall.

```bash
sudo ufw enable
```

Before enabling UFW on a remote server, make sure SSH access has already been explicitly allowed.

---

## Time and NTP

### `timedatectl`

Show system time, timezone and synchronization state.

```bash
timedatectl
```

### `timedatectl list-timezones`

List available timezones.

```bash
timedatectl list-timezones
```

### `timedatectl set-timezone <timezone>`

Change the system timezone.

```bash
sudo timedatectl set-timezone Europe/Kyiv
```

### `chronyc tracking`

Show the current Chrony/NTP synchronization state.

```bash
chronyc tracking
```

### `chronyc sources -v`

Show configured NTP sources and their status.

```bash
chronyc sources -v
```

Useful when checking whether the server is actually synchronizing with an NTP source.

---

## Packages

### `apt update`

Refresh package repository metadata.

```bash
sudo apt update
```

This does not upgrade installed packages.

### `apt upgrade`

Install available updates for installed packages.

```bash
sudo apt upgrade
```

### `apt list --upgradable`

Show packages that have available updates.

```bash
apt list --upgradable
```

### `dpkg -l`

List installed Debian packages.

```bash
dpkg -l
```

Useful when checking whether a specific package is installed.

### `dpkg -l | grep <name>`

Search installed packages by name.

```bash
dpkg -l | grep nginx
```

### `apt policy <package>`

Show installed/candidate versions and repository information.

```bash
apt policy nginx
```

Useful when investigating package versions.

---

## Snap

### `snap list`

Show installed Snap packages.

```bash
snap list
```

### `snap services`

Show services provided by Snap packages.

```bash
snap services
```

Useful when investigating what Snap applications are running in the background.

---

## Processes and resources

### `ps aux`

Show running processes.

```bash
ps aux
```

### `ps aux | grep <name>`

Search for a specific process.

```bash
ps aux | grep nginx
```

### `top`

Interactive view of CPU, memory and running processes.

```bash
top
```

### `free -h`

Show RAM and swap usage in human-readable units.

```bash
free -h
```

### `df -h`

Show filesystem disk usage.

```bash
df -h
```

### `du -sh <directory>`

Show the size of a directory.

```bash
du -sh /var/log
```

This is particularly useful when investigating what is consuming disk space.

---

## Files and permissions

### `ls -lah`

List files with permissions, ownership, hidden files and human-readable sizes.

```bash
ls -lah
```

### `stat <file>`

Show detailed file metadata.

```bash
stat /etc/ssh/sshd_config
```

### `chmod`

Change file permissions.

```bash
chmod 600 ~/.ssh/authorized_keys
```

### `chown`

Change file owner and group.

```bash
sudo chown denis:denis <file>
```

### `find`

Search for files and directories.

```bash
find /etc -name "*.conf"
```

Very useful, but the syntax is worth learning because it becomes extremely powerful.

---

## Text and configuration inspection

### `cat`

Print a file.

```bash
cat /etc/hostname
```

### `less`

Read a file interactively without loading the whole thing into your terminal.

```bash
less /var/log/syslog
```

Useful shortcuts:

- `Space` → next page
- `b` → previous page
- `/text` → search
- `q` → quit

### `grep`

Search for text.

```bash
grep "PasswordAuthentication" /etc/ssh/sshd_config
```

### `grep -R`

Search recursively through directories.

```bash
grep -R "PasswordAuthentication" /etc/ssh/
```

### `tail`

Show the end of a file.

```bash
tail -n 50 /var/log/syslog
```

### `tail -f`

Follow a file as it changes.

```bash
tail -f /var/log/syslog
```

---

## Git

### `git status`

Show modified, staged and untracked files.

```bash
git status
```

### `git diff`

Show unstaged changes.

```bash
git diff
```

### `git log --oneline`

Show a compact commit history.

```bash
git log --oneline
```

### `git add <file>`

Stage a file for commit.

```bash
git add docs/server.md
```

### `git commit`

Create a commit.

```bash
git commit -m "Update server documentation"
```

### `git push`

Push local commits to the remote repository.

```bash
git push
```

Recommended workflow:

```text
Change
  ↓
Verify
  ↓
Document
  ↓
Sanitize
  ↓
git status
  ↓
git diff
  ↓
git add
  ↓
git commit
  ↓
git push
```

---

## Useful command combinations

### Find a service listening on a port

```bash
sudo ss -tulpen | grep :22
```

### Check whether a service exists and is running

```bash
systemctl status <service>
```

### Check failed services and recent errors

```bash
systemctl --failed
journalctl -p err -b
```

### Check network interface + IP + routes

```bash
ip -br link
ip -br addr
ip route
```

### Check NetworkManager state

```bash
nmcli device status
nmcli connection show
```

### Check an Ethernet interface

```bash
sudo ethtool <interface>
sudo ethtool -i <interface>
```

### Check SSH configuration

```bash
sudo sshd -t
sudo sshd -T
```

### Check firewall

```bash
sudo ufw status verbose
sudo ufw status numbered
```

### Quick server health check

```bash
uptime
free -h
df -h
systemctl --failed
sudo ss -tulpen
```

---

## Commands worth memorizing

These are the commands I expect to use frequently enough that they are worth learning rather than constantly looking up:

```bash
ip -br addr
ip route
nmcli device status
nmcli connection show
sudo ethtool <interface>
sudo ss -tulpen
systemctl status <service>
systemctl --failed
journalctl -u <service>
sudo sshd -t
sudo sshd -T
sudo ufw status verbose
timedatectl
chronyc tracking
apt update
apt list --upgradable
df -h
free -h
git status
git diff
```

The goal is to gradually move these commands from the reference into memory. The rest can remain as a practical lookup reference.
