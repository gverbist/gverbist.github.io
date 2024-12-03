+++
title = "Configuring SSH on your CISCO Switch"
date = "2024-12-03T20:47:57+01:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Bist"
authorTwitter = "" #do not include @
cover = "/ciscossh.png"
tags = ["linux", "cisco"]
keywords = ["", ""]
description = "The definitive guide to configuring ssh on your cisco switch"
showFullContent = false
readingTime = false
hideComments = false
+++

# Configuring SSH on a Cisco 2960 Switch

Configuring Secure Shell (SSH) on a Cisco Catalyst 2960 switch enhances security by encrypting remote administrative access, effectively replacing less secure protocols like Telnet. This guide provides a step-by-step approach to setting up SSH on your Cisco 2960 switch.

---

## Prerequisites

Before configuring SSH, ensure the following:

- **User Authentication**: Configure user authentication for local or remote access.
- **Hostname and Domain Name**: Assign a unique hostname and domain name to the switch.
- **RSA Key Pair**: Generate an RSA key pair for encryption purposes.

---

## Configuration Steps

### 1. Access the Switch
Connect to the switch using a console cable and access the command-line interface (CLI).

### 2. Enter Global Configuration Mode


```
enable
configure terminal
```
## 3. Set the Hostname

Assign a unique hostname to identify the switch:
```
hostname [Your_Switch_Name]
```
## 4. Configure the Domain Name

Set the domain name for the switch:
```
ip domain-name [your_domain.com]
```
## 5. Generate the RSA Key Pair

Create the RSA keys necessary for SSH encryption:
```
crypto key generate rsa
```
When prompted, specify a modulus size of at least 1024 bits for enhanced security.
## 6. Configure User Authentication

Define a local user account with a secure password:
```
username [admin] privilege 15 secret [Your_Strong_Password]
```
## 7. Enable SSH on Virtual Terminal (VTY) Lines

Restrict VTY lines to accept only SSH connections:
```
line vty 0 15
transport input ssh
login local
```
## 8. Specify the SSH Version

Configure the switch to use SSH version 2 for better security:
```
ip ssh version 2
```
## 9. Set SSH Parameters (Optional)

Adjust SSH timeouts and authentication retries as needed:
```
ip ssh timeout [120]
ip ssh authentication-retries [3]
```
## 10. Save the Configuration

Ensure all settings are saved to the startup configuration:
```
copy running-config startup-config
```
### Verification

To confirm that SSH is configured correctly:

Check SSH Status:
```
show ip ssh
```

You were probably thinking this is it. And this is where all guides end the configuration. But there is more!

>
>NOW YOU NEED TO CONFIGURE YOUR SSH CLIENT!


## 11. Resolving SSH Authentication and Cipher Issues with Cisco 2960 Switches

When configuring SSH on a Cisco 2960 switch, you may encounter issues with SSH authentication or ciphers. These issues are typically due to compatibility mismatches between the SSH client and the ciphers or algorithms supported by the switch. Modern SSH clients often default to more secure encryption standards, which may not align with the older ciphers supported by the Cisco 2960 switch.

To resolve these compatibility issues, you need to adjust the configuration of your SSH client by adding specific parameters to your `~/.ssh/config` file.

---

### Required Configuration for `~/.ssh/config`

The following configuration needs to be added to your SSH client’s configuration file to ensure compatibility with the Cisco 2960 switch:

```plaintext
Host <cisco ip>
  Ciphers +aes128-cbc,3des-cbc,aes192-cbc,aes256-cbc  
  KexAlgorithms +diffie-hellman-group-exchange-sha1,diffie-hellman-group14-sha1,diffie-hellman-group1-sha1  
  HostkeyAlgorithms +ssh-rsa
```

## 12. Connecting with SSH

Now connect with:
```
ssh -l <username> <ip address switch>
```
