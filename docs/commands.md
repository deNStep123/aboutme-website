## System

```bash
hostnamectl              # hostname, OS, kernel
uname -a                  # kernel/system info
uptime                    # uptime + load
timedatectl               # time, timezone, NTP status
```

## Systemd / Services

```bash
systemctl status <svc>    # service status
systemctl start <svc>     # start service
systemctl stop <svc>      # stop service
systemctl restart <svc>   # restart service
systemctl enable <svc>    # enable at boot
systemctl is-active <svc> # active/inactive
systemctl is-enabled <svc># enabled/disabled
systemctl --failed        # failed units
```

## Logs

```bash
journalctl                # system logs
journalctl -u <svc>       # service logs
journalctl -u <svc> -f    # follow service logs
journalctl -b             # current boot logs
journalctl -b -1          # previous boot logs
journalctl -p err -b      # errors from current boot
```

## Network

```bash
ip -br link               # interfaces + link state
ip -br addr               # interfaces + IPs
ip route                  # routing table
ip neigh                  # ARP/neighbors
ss -tulpen                # listening ports + processes
```

## NetworkManager

```bash
nmcli device status       # device state
nmcli connection show     # connection profiles
nmcli device show <if>    # interface details
nmcli connection show <x>  # profile details
```

## Ethernet

```bash
ethtool <if>              # link, speed, duplex, autoneg
ethtool -i <if>           # NIC driver/hardware
ip link set <if> up       # bring interface up
```

## Netplan

```bash
netplan get              # effective config
netplan generate         # validate/generate config
netplan try              # temporary apply + rollback
netplan apply            # apply config
```

⚠️ `netplan apply` can break SSH on a remote server if the configuration is wrong.

## SSH

```bash
ssh <user>@<host>                    # connect
ssh -i <key> <user>@<host>           # connect with key
ssh -o PubkeyAuthentication=no ...   # test without key auth
sshd -t                              # validate config
sshd -T                              # show effective config
```

## Firewall (UFW)

```bash
ufw status                 # firewall state
ufw status verbose         # state + policies
ufw status numbered        # rules + numbers
ufw allow <port>/tcp       # allow TCP port
ufw delete <rule>          # remove rule
ufw enable                 # enable firewall
```

⚠️ Allow SSH before enabling UFW on a remote server.

## Time / NTP

```bash
timedatectl                # time + timezone + sync
timedatectl list-timezones # available timezones
chronyc tracking           # NTP synchronization
chronyc sources -v         # NTP sources
```

## Packages

```bash
apt update                 # refresh repositories
apt upgrade                # install updates
apt list --upgradable      # available updates
apt policy <pkg>           # package/version info
dpkg -l                    # installed packages
dpkg -l | grep <name>      # find installed package
snap list                  # installed snaps
snap services              # snap services
```

## Processes / Resources

```bash
ps aux                     # running processes
ps aux | grep <name>       # find process
top                        # live CPU/RAM/process view
free -h                    # RAM + swap
df -h                      # filesystem usage
du -sh <dir>               # directory size
```

## Files / Permissions

```bash
ls -lah                    # files + permissions + sizes
stat <file>                # file metadata
find <path> -name <name>   # find files
chmod <mode> <file>        # change permissions
chown <user>:<group> <file># change owner
```

## Text / Logs

```bash
cat <file>                 # print file
less <file>                # read file
grep <text> <file>         # search text
grep -R <text> <path>      # recursive search
tail -n <N> <file>         # last N lines
tail -f <file>             # follow file
```

## Git

```bash
git status                 # repo state
git diff                   # changes
git log --oneline          # commit history
git add <file>             # stage changes
git commit -m "<msg>"      # create commit
git push                   # push to remote
```

Typical workflow:

```text
change → verify → sanitize → status → diff → add → commit → push
```

## Quick Diagnostics

```bash
# System
uptime
free -h
df -h
systemctl --failed

# Network
ip -br link
ip -br addr
ip route
ss -tulpen

# Services / logs
systemctl status <svc>
journalctl -u <svc>

# SSH
sshd -t
sshd -T

# Firewall
ufw status verbose
```

## Commands Worth Memorizing

```text
ip -br addr          → IPs / interfaces
ip route             → routing
nmcli device status  → NetworkManager state
ethtool <if>         → physical link
ss -tulpen           → listening ports
systemctl --failed   → failed services
journalctl -u <svc>  → service logs
sshd -t              → SSH config syntax
sshd -T              → effective SSH config
ufw status numbered  → firewall rules
chronyc tracking     → NTP state
df -h                → disk
free -h              → RAM
```
