+++
title = "Saving Bookmarks with a Single Keystroke: Self-Hosting Linkding on a Raspberry Pi"
date = "2025-11-28T16:34:00+01:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Bist"
authorTwitter = "" #do not include @
cover = ""
tags = ["", ""]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
+++


Browser bookmarks are a mess. They're locked into whichever browser you're using, synced to Google or Mozilla's servers, and scattered across devices with no unified way to search them. As part of my ongoing project to move away from Big Tech services and toward self-hosted alternatives, I set up Linkding — a minimal, fast bookmark manager — and wired it up to save URLs with a single keyboard shortcut.

Here's how I did it.

## Why Linkding?

There are several self-hosted bookmark managers out there (Wallabag, Shiori, Shaarli), but Linkding stood out for a few reasons:

- It's lightweight and runs perfectly on a Raspberry Pi 4
- Clean, minimal interface without unnecessary features
- Built-in REST API for automation
- Automatic fetching of titles and descriptions
- Tags and search that actually work

It does one thing and does it well.

## Setting Up Linkding with Docker

I run most of my self-hosted services via Docker Compose on a Raspberry Pi 4. Linkding's image supports ARM natively, so there's no emulation overhead.

Create a directory and add a `docker-compose.yml`:

```yaml
services:
  linkding:
    image: sissbruecker/linkding:latest
    container_name: linkding
    restart: unless-stopped
    ports:
      - "9090:9090"
    volumes:
      - ./data:/etc/linkding/data
    environment:
      - LD_SUPERUSER_NAME=admin
      - LD_SUPERUSER_PASSWORD=changeme
```

Then spin it up:

```bash
docker compose up -d
```

Access the web interface at `http://<pi-ip>:9090`, log in, and immediately change your password. Linkding uses SQLite by default, so there's no database container to manage — your entire bookmark collection lives in that `./data` directory.

## The Problem: Friction

Linkding has a browser extension, but I wanted something faster. My workflow is: I'm reading something, I want to save it, I don't want to click through menus. The ideal solution is copying the URL and hitting a keyboard shortcut.

Linux (especially with KDE on my Tuxedo OS setup) makes this surprisingly easy.

## Building the Keyboard Shortcut

The approach is simple:

1. Copy a URL to the clipboard
2. Press a shortcut
3. A script grabs the clipboard contents and POSTs to Linkding's API
4. A desktop notification confirms success

### Step 1: Get an API Token

In Linkding, go to Settings → Integrations → REST API and generate a token. Keep this safe.

### Step 2: The Script

I created `~/.local/bin/linkding-save`:

```bash
#!/bin/bash

# Configuration
LINKDING_URL="http://192.168.1.100:9090"  # Your Linkding instance
API_TOKEN="your-api-token-here"

# Get URL from clipboard (Wayland)
URL=$(wl-paste 2>/dev/null)

# Fallback to X11 if wl-paste fails
if [[ -z "$URL" ]]; then
    URL=$(xclip -selection clipboard -o 2>/dev/null)
fi

# Validate we have something that looks like a URL
if [[ ! "$URL" =~ ^https?:// ]]; then
    notify-send -u critical "Linkding" "Clipboard doesn't contain a valid URL"
    exit 1
fi

# Post to Linkding API
response=$(curl -s -w "\n%{http_code}" -X POST "${LINKDING_URL}/api/bookmarks/" \
    -H "Authorization: Token ${API_TOKEN}" \
    -H "Content-Type: application/json" \
    -d "{\"url\": \"${URL}\"}")

http_code=$(echo "$response" | tail -n1)
body=$(echo "$response" | sed '$d')

if [[ "$http_code" =~ ^2 ]]; then
    title=$(echo "$body" | grep -o '"title":"[^"]*"' | cut -d'"' -f4 | head -c 50)
    notify-send -u normal "Linkding" "Saved: ${title:-$URL}"
else
    notify-send -u critical "Linkding" "Failed to save bookmark (HTTP ${http_code})"
fi
```

Make it executable:

```bash
chmod +x ~/.local/bin/linkding-save
```

The script handles both Wayland (`wl-paste`) and X11 (`xclip`) clipboard access, validates that the clipboard actually contains a URL, and provides feedback via desktop notifications.

### Step 3: Dependencies

On Ubuntu/Tuxedo OS:

```bash
sudo apt install wl-clipboard libnotify-bin curl
```

### Step 4: KDE Shortcut

1. Open System Settings → Shortcuts → Custom Shortcuts
2. Edit → New → Global Shortcut → Command/URL
3. Name: "Save to Linkding"
4. Trigger: Set your preferred shortcut (I use `Meta+B`)
5. Action: `/home/yourusername/.local/bin/linkding-save`

## The Result

Now my workflow is:

1. See interesting article
2. `Ctrl+L` to select the URL bar, `Ctrl+C` to copy
3. `Meta+B` to save
4. Notification pops up: "Saved: Article Title Here"

Total time: under two seconds, hands never leave the keyboard.

## What's Next

This setup handles the basic "save URL" case. Linkding's API also supports adding tags, descriptions, and marking bookmarks as unread. A more advanced version of this script could pop up a dialog (using `kdialog` or `zenity`) to add tags before saving.

For now, the friction-free capture is what matters. I can always organize later through the web interface.

## Backups

Since Linkding uses SQLite, backing up is straightforward — just include the `./data` directory in your regular backup routine. I use Restic to push encrypted snapshots to Backblaze B2 nightly. One less thing living on someone else's servers.

---

*This post is part of a series on moving away from Big Tech services toward self-hosted alternatives. I'm running Linkding, Mailcow, and other services on a Raspberry Pi 4 and a small VPS.*
