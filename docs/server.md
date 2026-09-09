## Base system
- OS: Ubuntu 26.04.1 LTS
- Time zone: Europe/Kyiv
- Time service: chrony
- NTP synchronization confirmed
- System updated and rebooted
- No failed systemd units detected after reboot

## SSH hardening

SSH is configured to use public-key authentication for remote administration.

Current security configuration:

- SSH port: `22`
- Public-key authentication: enabled
- Password authentication: disabled
- Keyboard-interactive authentication: disabled
- Root SSH login: disabled
- PAM: enabled

The hardening configuration is maintained separately in:

`/etc/ssh/sshd_config.d/99-hardening.conf`

The configuration was validated with:

```bash
sudo sshd -t
```

The effective SSH configuration was verified with:

```bash
sudo sshd -T
```

The following values were confirmed:

```text
permitrootlogin no
pubkeyauthentication yes
passwordauthentication no
kbdinteractiveauthentication no
```

### Authentication verification

Key-based login was successfully tested from a Windows client.

A second test explicitly disabled public-key authentication:

```powershell
ssh -o PubkeyAuthentication=no <USERNAME>@<PUBLIC_IP>
```

The server rejected the connection with:

```text
Permission denied (publickey).
```

This confirmed that password-based SSH authentication is no longer available.

The existing SSH session was kept open while the configuration was applied and the new authentication method was tested, reducing the risk of losing remote access.
