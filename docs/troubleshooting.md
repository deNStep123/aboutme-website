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
