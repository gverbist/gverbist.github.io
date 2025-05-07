+++
title = "Hardening and Mfa on Linux"
date = "2025-05-07T16:32:08+02:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Bist"
authorTwitter = "" #do not include @
cover = "/hardening.png"
tags = ["linux", "hardening", "ssh", "security", "2FA", "MFA"] 
keywords = ["", ""]
description = "Hardening Linux and Setup 2FA"
showFullContent = false
readingTime = false
hideComments = false
+++

# Hardening and MFA on Linux

This guide walks through setting up a hardened VPS with secure SSH access and two-factor authentication (2FA) using Google Authenticator.

## Generate SSH Keys

On your local machine:
```bash
ssh-keygen -t rsa -b 4096 -f <name-of-the-certificate>
```

## VPS Setup

### Add a New Sudo User

```bash
ssh root@<vps-ip-address>
adduser hades
usermod -aG sudo hades
su - hades
```

### Set Up SSH Keys

```bash
mkdir ~/.ssh
echo '<vps-ssh.pub>' > ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

## VPS Hardening

### Update the System

```bash
sudo apt update -y && sudo apt full-upgrade -y && sudo apt autoremove -y && sudo apt autoclean -y
```

### SSH Hardening

Edit `/etc/ssh/sshd_config` and configure as follows:

```text
LogLevel VERBOSE
PermitRootLogin no
MaxAuthTries 3
MaxSessions 5
HostbasedAuthentication no
PermitEmptyPasswords no
UsePAM yes
X11Forwarding no
PrintMotd no
ClientAliveInterval 600
ClientAliveCountMax 0
AllowUsers hades
Protocol 2
PasswordAuthentication no
AuthenticationMethods publickey,keyboard-interactive
```

### Install Fail2Ban

```bash
sudo apt install fail2ban -y
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.conf.bak
```

Add/edit `/etc/fail2ban/jail.conf`:

```ini
[sshd]
enabled = true
bantime = 4w
maxretry = 3
```

## Configure 2FA with Google Authenticator

### Install the PAM module

```bash
sudo apt install libpam-google-authenticator -y
google-authenticator
```

Follow the prompts to configure:

Read the follow up questions, but probably answer yes to them all.

### PAM Configuration for SSH

```bash
sudo cp /etc/pam.d/sshd /etc/pam.d/sshd.bak
sudo vim /etc/pam.d/sshd
```

Edit:
```text
#@include common-auth
auth required pam_google_authenticator.so
auth required pam_permit.so
```

### Restart SSH

```bash
sudo service ssh restart
```

## Test 2FA SSH Login

```bash
ssh hades@VPS -i ~/.ssh/vps-ssh
# Enter passphrase and 2FA code
```

## Transfer Files to VPS

```bash
scp -i ~/.ssh/vps-ssh -r ~/Pentesting hades@VPS:~/
```

---

Sources:
- HTB course
- [man sshd_config](https://man.openbsd.org/sshd_config)
- [Google Authenticator GitHub](https://github.com/google/google-authenticator-libpam)
- [Multi-factor Authentication Wikipedia](https://en.wikipedia.org/wiki/Multi-factor_authentication)

