+++
title = "Backing Up Self-Hosted Docker Services with Restic"
date = "2025-11-28T21:33:26+01:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Bist"
authorTwitter = "" #do not include @
cover = ""
tags = ["Self-Hosted", "linux"]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
+++
# Backing Up Self-Hosted Docker Services with Restic

Self-hosting is great until you lose everything. A dead SD card, a botched upgrade, or a misconfigured volume mount can wipe out months of data in seconds. If you're running services like Nextcloud, Immich, or Linkding on a Raspberry Pi or home server, you need a backup strategy that actually works.

I use Restic with a Hetzner Storage Box. Here's how I set it up.

## Why Restic?

There are plenty of backup tools out there. Restic stands out for a few reasons:

- Deduplication — only changed data gets uploaded after the first backup
- Encryption — data is encrypted before it leaves your machine
- Multiple backends — works with local drives, SFTP, S3, Backblaze B2, and more
- Fast restores — browse and restore individual files without downloading everything

It's also simple. No daemons, no complex configuration files. Just a single binary and a few commands.

## Why Hetzner Storage Box?

I wanted offsite backups but didn't want to rely on big cloud providers. Hetzner Storage Box is cheap (€3.81/month for 1TB), based in Europe, and supports SFTP out of the box. Since Restic encrypts everything client-side, Hetzner only ever sees encrypted blobs.

There's a certain irony in escaping Big Tech only to store backups on someone else's server. But the encryption means I'm not trusting them with my data — just with storing opaque files. If Hetzner disappeared tomorrow, I'd lose access to my offsite backups but not my privacy.

## My Setup

I run Docker services on a Raspberry Pi 4. Each application lives in its own directory:

```
/home/bist/
├── linkding/
│   ├── docker-compose.yml
│   └── data/
├── immich/
│   ├── docker-compose.yml
│   └── data/
└── nextcloud/
    ├── docker-compose.yml
    └── data/
```

Config and data together, per application. This makes backups straightforward — grab the whole folder and you have everything needed to restore that service.

## Setting Up Restic with Hetzner

### Install Restic

```bash
sudo apt install restic
```

### Configure SSH Access

Hetzner Storage Box uses SFTP on port 23. First, upload your SSH key:

```bash
echo "mkdir .ssh" | sftp -P 23 uXXXXXX@uXXXXXX.your-storagebox.de
scp -P 23 ~/.ssh/id_rsa.pub uXXXXXX@uXXXXXX.your-storagebox.de:.ssh/authorized_keys
```

Test the connection:

```bash
sftp -P 23 uXXXXXX@uXXXXXX.your-storagebox.de
```

If it connects without a password prompt, you're good.

### Initialize the Repository

Create a directory for your backups and initialize Restic:

```bash
echo "mkdir backups" | sftp -P 23 uXXXXXX@uXXXXXX.your-storagebox.de

export RESTIC_REPOSITORY="sftp:uXXXXXX@uXXXXXX.your-storagebox.de:23:/backups"
export RESTIC_PASSWORD="your-strong-encryption-password"

restic init
```

Pick a strong password. Write it down somewhere safe — a password manager, a printed copy in a drawer, wherever. If you lose this password, your backups are gone. Restic has no recovery mechanism.

### Create an Environment File

Rather than exporting variables every time, create a file:

`~/.restic-env`:

```bash
export RESTIC_REPOSITORY="sftp:uXXXXXX@uXXXXXX.your-storagebox.de:23:/backups"
export RESTIC_PASSWORD="your-strong-encryption-password"
```

Lock it down:

```bash
chmod 600 ~/.restic-env
```

Now you can just run `source ~/.restic-env` before any Restic command.

## The Backup Script

I wrote a simple script that backs up each application directory:

```bash
#!/bin/bash

set -e

export RESTIC_REPOSITORY="sftp:uXXXXXX@uXXXXXX.your-storagebox.de:23:/backups"
export RESTIC_PASSWORD="your-strong-encryption-password"

BACKUP_DIRS=(
    "/home/bist/linkding"
    "/home/bist/immich"
    "/home/bist/nextcloud"
)

LOG_FILE="/home/bist/logs/restic-backup.log"
mkdir -p "$(dirname "$LOG_FILE")"

log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

log "=== Starting backup ==="

for dir in "${BACKUP_DIRS[@]}"; do
    if [[ -d "$dir" ]]; then
        log "Backing up: $dir"
        restic backup "$dir" --exclude="*.log" --exclude="*.tmp"
    else
        log "Warning: $dir does not exist, skipping"
    fi
done

log "Pruning old snapshots..."
restic forget --keep-daily 7 --keep-weekly 4 --keep-monthly 6 --prune

log "=== Backup complete ==="

restic snapshots --latest 3
```

Adding a new service means adding one line to the `BACKUP_DIRS` array.

The retention policy keeps the last 7 daily backups, 4 weekly, and 6 monthly. Old snapshots get pruned automatically, so storage doesn't grow forever.

## Automating with Cron

I run backups nightly at 3 AM:

```bash
crontab -e
```

```
0 3 * * * /home/bist/scripts/backup.sh
```

Logs go to `/home/bist/logs/restic-backup.log` so I can check what happened if something seems off.

## Restoring

Backups are worthless if you can't restore them. Here's how.

### List Snapshots

```bash
source ~/.restic-env
restic snapshots
```

This shows all your backups with timestamps and IDs.

### Restore an Application

Always restore to a temporary location first:

```bash
restic restore latest --target /tmp/restore --include "/home/bist/linkding"
```

Check it looks right, then swap it into place:

```bash
cd /home/bist/linkding && docker compose down
mv /home/bist/linkding /home/bist/linkding.broken
mv /tmp/restore/home/bist/linkding /home/bist/
docker compose up -d
```

Once you've verified everything works, delete the broken copy.

### Restore a Single File

```bash
restic restore latest --target /tmp/restore --include "/home/bist/linkding/docker-compose.yml"
```

### Browse Backups Interactively

Restic can mount snapshots as a filesystem:

```bash
mkdir -p /tmp/restic-mount
restic mount /tmp/restic-mount
```

Browse with your file manager or terminal, copy what you need, then unmount.

## The Most Important Step

Test your restores. A backup you've never tested is just a hope. Once a month or so, I pick a random service and restore it to `/tmp` just to make sure everything actually works.

## What About Databases?

SQLite-based services (like Linkding) are fine — the database file gets backed up with everything else.

For services with MySQL or PostgreSQL, you should dump the database before the backup runs. For example, with Mailcow:

```bash
./helper-scripts/backup_and_restore.sh backup all
```

Then include the dump directory in your backup paths.

## The 3-2-1 Rule

This setup gives me:

- **3 copies**: Live data + local snapshots + Hetzner
- **2 media types**: SD card + cloud storage
- **1 offsite**: Hetzner Storage Box

If my Pi dies, I buy a new one and restore from Hetzner. If Hetzner dies, I still have local data. If my house burns down... well, at least my bookmarks survive.

---

*This post is part of a series on self-hosting and moving away from Big Tech services.*
