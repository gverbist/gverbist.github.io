+++
date = '2024-11-28T21:25:06+01:00'
draft = false
title = 'Apps'
+++
# Apps I use

Hey there! I thought it’d be fun to share the apps that keep my daily work (and life) running smoothly. Whether it’s tackling technical challenges, or just staying on top of everything, these tools are my go-to. I’m big on self-hosted apps for privacy and control, but I also mix in some other handy tools to keep things efficient and stress-free. If you’re curious about what makes my day tick, keep reading!

- Tuba - Mastodon client

- Yazi - File Manager CLI 

- Geek-life   - Todo list 

- Portainer - Container Management Platform

- Proxmox - Virtualization Host

- Mailcow - Self hosted mail server

- Neovim 

- VimWiki - personal documentation

- Arch Linux and Artix Linux - primary OS


# Self Hosted Apps

I’ve set up my own self-hosted software stack to keep more control over my data, boost security, and cut down on subscriptions to external services. Instead of relying on big cloud providers, I run everything I need right from my own servers.

This approach gives me complete privacy and lets me customize each tool exactly how I want. Plus, self-hosting is a great way to learn more about how these tools work behind the scenes, and I find it rewarding to build and manage my own tech environment.

I run all my self-hosted services in Docker containers, deploying each one with Docker Compose files for easy configuration and management. These Compose files, stored on github, define everything from service details to network setups. It keeps things organized and makes it easy to spin up or update services.

Everything runs on a Proxmox host with several virtual machines, each running Docker for individual services. This setup keeps things isolated and manageable, while Proxmox handles the VMs, adding a layer of flexibility and control.

For backups, I use Restic in a Docker container, with backups sent to a separate Synology NAS. This way, all my critical data is securely backed up, and Restic’s deduplication keeps storage efficient. The combination of Proxmox, Docker, and Restic provides a robust and flexible self-hosted environment with data resilience and straightforward recovery options if needed.

## List of self hosted services:

### Mailcow: The Best Open-Source Email Stack for Self-Hosting

When it comes to self-hosting email, Mailcow stands out as the go-to open-source stack. It’s a complete package with all the essential tools you need for email hosting: webmail, spam filtering, and easy management features. Mailcow is built to handle everything a typical email setup would require, and it’s designed to work well right out of the box—something rare in the world of self-hosted email.

#### Why Mailcow?

Mailcow brings together everything needed to run a fully functional email server, like Postfix for mail transfer, Dovecot for mail retrieval, and Rspamd for spam filtering. Plus, the UI is straightforward, making it easy to manage domains, mailboxes, and users. Unlike trying to set up each of these services independently, Mailcow takes care of all the integration, so you’re not stuck troubleshooting endless compatibility issues.

#### The Challenge: Finding a Clean IP

One of the biggest hurdles for any self-hosted email server is getting a clean IP—one that isn’t blacklisted or likely to trigger spam filters. Many ISPs and cloud providers recycle IP addresses, and there’s a decent chance your IP has been flagged or abused before, which could get your emails sent straight to spam or blocked altogether. That can be a real challenge when you’re just trying to run a clean, legitimate email setup.

To avoid this, you need to:

Check if your IP is listed on any blacklists using tools like MXToolbox.

Register a reverse DNS record with your ISP to match your domain.

Authenticate with SPF, DKIM, and DMARC to prove to receiving servers that your emails are legit.

Mailcow Helps but Can't Do It All

Mailcow does a lot to make setup easy and secure, but getting reliable email delivery with a self-hosted server is still a bit of an art. It’s all about setting up correctly, using a clean IP, and managing ongoing issues like spam filtering and occasional IP blocks. But once it’s up and running, Mailcow makes self-hosting email surprisingly smooth and secure.
