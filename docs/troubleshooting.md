## SSH hardening: cloud-init configuration override

### Problem

Password authentication remained enabled even after adding a dedicated SSH hardening configuration.

The effective configuration showed:

```text
passwordauthentication yes
```

### Investigation

The SSH configuration was searched across the main configuration file and included configuration directory:

```bash
sudo grep -RniE '^\s*(PermitRootLogin|PasswordAuthentication|PubkeyAuthentication|KbdInteractiveAuthentication)\b' /etc/ssh/sshd_config /etc/ssh/sshd_config.d/
```

The investigation found conflicting settings:

```text
/etc/ssh/sshd_config.d/50-cloud-init.conf: PasswordAuthentication yes
/etc/ssh/sshd_config.d/99-hardening.conf: PasswordAuthentication no
```

The cloud-init configuration was taking precedence for this option.

### Resolution

The conflicting `PasswordAuthentication yes` setting was removed from the cloud-init configuration, while the dedicated `99-hardening.conf` file was retained as the explicit SSH hardening configuration.

The resulting configuration was validated with:

```bash
sudo sshd -t
```

and the effective configuration was checked with:

```bash
sudo sshd -T
```

The final state confirmed:

```text
passwordauthentication no
permitrootlogin no
pubkeyauthentication yes
kbdinteractiveauthentication no
```

### Verification

A normal key-based SSH login succeeded.

A connection with public-key authentication explicitly disabled was rejected:

```text
Permission denied (publickey).
```

This confirmed that SSH password authentication was successfully disabled without losing key-based remote access.

### Backup route through secondary interface

Problem:
Traffic to the planned backup endpoint was initially routed through the primary external interface.

Investigation:
- Checked the routing table with `ip route`.
- Checked route selection with `ip route get`.
- Verified connectivity to the office gateway.
- Confirmed that `enp2s0` is the working path to the office LAN.
- Added a temporary host route through `192.168.1.1`.
- Verified the new route with `ip route get`.
- Tested connectivity to the backup endpoint.

Result:
The server correctly selected `enp2s0` and `192.168.1.1` for the test destination. The gateway then returned `Destination Net Unreachable`.

Conclusion:
The Linux server-side routing test succeeded. Further investigation is required on the pfSense side to establish the complete backup path, including routing, NAT/port forwarding and return traffic.

Next step:
Confirm the exact pfSense configuration and backup SSH port before making the route persistent.
