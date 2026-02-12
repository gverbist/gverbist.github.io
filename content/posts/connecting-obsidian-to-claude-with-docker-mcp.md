+++
title = "Connecting Obsidian to Claude Code with Docker's MCP Toolkit"
date = "2026-02-12"
author = "Bist"
authorTwitter = ""
cover = ""
tags = ["Docker", "MCP", "Obsidian", "Claude", "Linux", "AI", "Self-Hosted"]
keywords = ["MCP", "Docker", "Obsidian", "Claude Code", "MCP Toolkit"]
description = "How to set up Docker Desktop's MCP Toolkit on Linux and connect your Obsidian vault to Claude Code — giving your AI assistant direct access to read, write, and search your notes."
showFullContent = false
readingTime = true
hideComments = false
+++

There's a moment where your note-taking workflow clicks into place. For me, that moment was when I gave Claude direct access to my Obsidian vault. Not through copy-pasting. Not through file uploads. A live, two-way connection where Claude can read my notes, create new ones, and search across everything I've written — all from the terminal.

The glue that makes this work is the **Model Context Protocol (MCP)**, and Docker just made it ridiculously easy to set up.

## What Is MCP and Why Should You Care?

MCP is an open protocol that standardizes how AI applications talk to external tools and data sources. Think of it as a USB-C port for AI — one standard interface that connects to anything.

Without MCP, getting Claude to interact with your Obsidian vault means manually feeding it context every single time. With MCP, Claude has persistent, structured access to your notes. It can look things up, cross-reference documentation, and write new content directly into your vault.

The architecture looks like this:

```
Claude Code ── MCP Protocol ──▶ Docker MCP Gateway ── Container ──▶ mcp/obsidian ── REST API ──▶ Obsidian
```

Docker's MCP Gateway sits in the middle and manages everything. It handles server lifecycle, secrets, and exposes all your enabled MCP servers through one unified endpoint. No Python virtual environments. No dependency management. Just containers.

## What You'll Need

Before we start, make sure you have:

- A Linux machine with a desktop environment (GNOME, KDE, or MATE)
- CPU virtualization enabled (Intel VT-x or AMD-V)
- KVM support
- At least 4 GB RAM
- [Obsidian](https://obsidian.md) installed with a vault

## Step 1 — Install Docker Desktop

Docker Desktop on Linux runs inside a lightweight VM, which is why we need KVM. Load the kernel modules and give your user access:

```bash
sudo modprobe kvm
sudo modprobe kvm_intel   # Intel processors
# or
sudo modprobe kvm_amd     # AMD processors
```

```bash
sudo usermod -aG kvm $USER
```

Log out and back in after this.

Now install Docker Desktop for your distribution. Grab the latest package from the [official Docker Desktop page](https://docs.docker.com/desktop/setup/install/linux/) and install it:

**Debian / Ubuntu:**
```bash
sudo apt install ./docker-desktop-<version>-amd64.deb
```

**Fedora / RHEL:**
```bash
sudo dnf install ./docker-desktop-<version>-x86_64.rpm
```

**Arch:**
```bash
sudo pacman -U ./docker-desktop-<version>-x86_64.pkg.tar.zst
```

Launch it from your application menu or start it from the terminal:

```bash
systemctl --user start docker-desktop
```

Verify it's running:

```bash
docker --version
```

## Step 2 — Enable the MCP Toolkit

The MCP Toolkit is currently a beta feature in Docker Desktop. You need to toggle it on.

1. Open **Docker Desktop**
2. Click the **gear icon** (Settings)
3. Go to **Beta features**
4. Enable **Docker MCP Toolkit**
5. Click **Apply & restart**

Once Docker Desktop restarts, you'll see a new **MCP Toolkit** tab in the sidebar. That's where the magic happens — a built-in catalog of over 200 verified MCP servers you can enable with a click.

Verify the CLI works:

```bash
docker mcp --help
```

## Step 3 — Set Up the Obsidian Local REST API Plugin

The Obsidian MCP server doesn't talk to your vault files directly. It communicates through the **Local REST API** community plugin, which exposes your vault over a local HTTPS endpoint.

1. Open **Obsidian**
2. Navigate to **Settings** → **Community plugins**
3. Click **Turn on community plugins** if prompted
4. Click **Browse**, search for `Local REST API`
5. Click **Install**, then **Enable**

The plugin immediately:
- Generates a unique API key
- Creates a self-signed SSL certificate (valid for 365 days)
- Starts an HTTPS server on port **27124**

### Copy Your API Key

Go to **Settings** → **Community plugins** → **Local REST API** → **Options** and copy the API key shown at the top. You'll need this in the next step.

### Verify It's Working

```bash
curl -k https://127.0.0.1:27124 \
  -H "Authorization: Bearer YOUR_API_KEY"
```

The `-k` flag is needed because the plugin uses a self-signed certificate. If you get a response, the REST API is live.

The default ports:

| Protocol | Port  | Default State       |
|----------|-------|---------------------|
| HTTPS    | 27124 | Enabled             |
| HTTP     | 27123 | Disabled            |

## Step 4 — Enable the Obsidian MCP Server

Now we connect Docker's MCP Toolkit to your Obsidian vault.

### Using the Docker Desktop GUI

1. Open **Docker Desktop** → **MCP Toolkit**
2. Go to the **Catalog** tab
3. Search for **Obsidian**
4. Click the **+** icon to add it
5. Open the **Configuration** tab and enter your API key from Step 3

### Using the CLI

```bash
docker mcp server enable obsidian
```

Store your API key securely:

```bash
docker mcp secret set obsidian.api_key=YOUR_API_KEY
```

Confirm it's enabled:

```bash
docker mcp server ls
```

You should see `obsidian` in the list.

## Step 5 — Connect Claude Code

The Docker MCP Gateway exposes all your enabled servers through a single command: `docker mcp gateway run`. Claude Code just needs to know about it.

### Option A — Project-level configuration

Create a `.mcp.json` file in your project root:

```json
{
  "mcpServers": {
    "MCP_DOCKER": {
      "command": "docker",
      "args": ["mcp", "gateway", "run"],
      "type": "stdio"
    }
  }
}
```

This scopes the connection to that specific project.

### Option B — Global configuration

If you want Obsidian access in every Claude Code session:

```bash
claude mcp add --scope user MCP_DOCKER -- docker mcp gateway run
```

### Option C — Docker Desktop GUI

1. Open **Docker Desktop** → **MCP Toolkit** → **Clients** tab
2. Find **Claude Code** in the list
3. Click **Connect**

## Step 6 — Verify Everything Works

Start a fresh Claude Code session and check the connection:

```bash
claude mcp list
```

You should see:

```
MCP_DOCKER: docker mcp gateway run - ✓ Connected
```

Now ask Claude to list your vault files. If everything is wired up correctly, it will return your full vault structure.

### What You Can Do Now

With the connection active, Claude has access to these tools:

| Tool                  | What It Does                              |
|-----------------------|-------------------------------------------|
| `list_files_in_vault` | List all files and folders at vault root   |
| `list_files_in_dir`   | Browse a specific folder                   |
| `get_file_contents`   | Read any note                              |
| `simple_search`       | Full-text search across the vault          |
| `complex_search`      | Advanced queries using JsonLogic           |
| `append_content`      | Create new notes or append to existing     |
| `patch_content`       | Insert content under a specific heading    |
| `delete_file`         | Remove a note (requires confirmation)      |
| `get_periodic_note`   | Fetch daily, weekly, or monthly notes      |

You can list all available tools with:

```bash
docker mcp tools ls
```

## Troubleshooting

**MCP Toolkit tab not showing up?**
Make sure Docker Desktop is version **4.37.1 or later** and the beta feature is enabled under Settings → Beta features.

**Connection refused on port 27124?**
Obsidian needs to be running with the Local REST API plugin enabled. The MCP container connects to the host network, so the ports need to match.

**API key errors (401 responses)?**
Re-set the secret: `docker mcp secret set obsidian.api_key=YOUR_API_KEY`. You can also regenerate the key in Obsidian under **Settings** → **Local REST API** → **Reset All Cryptography**.

**Claude Code shows MCP_DOCKER as disconnected?**
Make sure Docker Desktop is running. Test the gateway manually with `docker mcp gateway run`. Check that the Obsidian server is listed in `docker mcp server ls`. Restart Claude Code after any config changes.

## What's Next

Once this is working, the Obsidian MCP server is just one of over 200 servers available in Docker's MCP Catalog. You can enable additional servers — GitHub, PostgreSQL, Slack, and many more — and they all funnel through the same gateway. One connection, all the tools.

If you want to go further and build your own custom MCP servers, check out [NetworkChuck's Docker MCP tutorial](https://github.com/theNetworkChuck/docker-mcp-tutorial) — it walks through building, containerizing, and deploying custom servers with an AI-assisted builder prompt.
