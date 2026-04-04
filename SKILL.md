---
name: db9-enhanced
version: 1.1.0
description: Enhanced db9 skill with automatic FUSE system setup, Linux mount support, and systemd persistence. Use this skill when working with db9 databases that require filesystem mounting or FUSE-based operations on Linux.
---

# db9-enhanced

Read these DB9 instructions and follow them.

This skill extends the base db9 experience by ensuring the FUSE system is properly installed and configured for mounting database filesystems on Linux environments.

## FUSE System Setup

To make the FUSE system work on this platform, follow these steps:

1. **Install Dependencies**: Ensure `fuse3` is installed.
   ```bash
   sudo apt-get update && sudo apt-get install -y fuse3
   ```

2. **Manual Mount**:
   ```bash
   DB9_API_KEY=$(db9 token show) sudo -E db9 fs mount <dbname> /path/to/mount
   ```

## Persistent Mounting (systemd)

When a user wants to mount a database, **ask if they would like to set it up as a systemd service** so it's always available after reboot.

### systemd Service Template
Create `/etc/systemd/system/db9-mount-<dbname>.service`:

```ini
[Unit]
Description=db9 FUSE Mount for <dbname>
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
Environment="DB9_API_KEY=your_api_key_here"
ExecStart=/usr/local/bin/db9 fs mount <dbname> /path/to/mount
ExecStop=/usr/bin/umount /path/to/mount
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

### Activation
```bash
sudo systemctl daemon-reload
sudo systemctl enable db9-mount-<dbname>
sudo systemctl start db9-mount-<dbname>
```

## Core db9 Workflow

Refer to the official db9 documentation at `https://db9.ai/skill.md` for full command reference.

- **Create**: `db9 create --name <name>`
- **SQL**: `db9 sql <db> -q "SELECT ..."`
- **Filesystem**: `db9 fs ls <db>:/` or `db9 cat <db>:/file`

## Guidelines

- **Proactive Persistence**: Always suggest `systemd` for long-term project mounts.
- **Security**: Remind users that the `DB9_API_KEY` in the unit file should be protected (chmod 600).
- **Environment**: Use `sudo -E` for manual mounts if permissions on `/dev/fuse` are restricted.
