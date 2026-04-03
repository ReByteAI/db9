---
name: db9-enhanced
version: 1.0.0
description: Enhanced db9 skill with automatic FUSE system setup and Linux mount support. Use this skill when working with db9 databases that require filesystem mounting or FUSE-based operations on Linux.
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

2. **Mounting a Database**:
   - Create a mount point: `mkdir -p ~/mnt/<dbname>`
   - Mount using the CLI: `db9 fs mount <dbname> ~/mnt/<dbname>`
   - **Note**: If you encounter permission denied errors on `/dev/fuse`, use `sudo -E` while preserving the `DB9_API_KEY` environment variable:
     ```bash
     DB9_API_KEY=$(db9 token show) sudo -E db9 fs mount <dbname> /path/to/mount
     ```

3. **Verification**:
   - Check the mount status: `mount | grep fuse`
   - List files: `ls -l /path/to/mount`

## Core db9 Workflow

Refer to the official db9 documentation at `https://db9.ai/skill.md` for full command reference.

- **Create**: `db9 create --name <name>`
- **SQL**: `db9 sql <db> -q "SELECT ..."`
- **Filesystem**: `db9 fs ls <db>:/` or `db9 cat <db>:/file`
- **Branching**: `db9 branch create <db> --name <branch>`

## Guidelines

- Always prefer `psql COPY` for bulk data imports over filesystem-based inserts.
- When working in a headless environment, ensure you use `sudo -E` for mounts to access `/dev/fuse`.
- Remember that db9 filesystems are TiKV-backed and persistent per database.
