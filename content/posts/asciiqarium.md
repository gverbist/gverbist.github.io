+++
title = "Asciiqarium - How To"
date = "2025-01-07T16:55:52+01:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = ""
authorTwitter = "" #do not include @
cover = "/fishies.png"
tags = ["linux"]
keywords = ["fishies", "asciiquarium"]
description = "How to install Asciiquarium on Arch Linux. The last screensaver you will need..."
showFullContent = false
readingTime = false
hideComments = false
+++
# How to Install Asciiquarium on Arch Linux Using `pacman`

**Asciiquarium**, a whimsical ASCII-art aquarium, is a fun addition to your terminal. Featuring swimming fish, sharks, submarines, and other sea creatures, it’s a lightweight and entertaining way to add personality to your Linux environment.

Good news for Arch Linux users: Asciiquarium is available in the official repositories, so you can install it directly using `pacman`. Here's how to do it step-by-step.

https://archlinux.org/packages/extra/any/asciiquarium/
---

## Step 1: Update Your System

Before installing any new packages, ensure your system is up to date. Open your terminal and run:

**`sudo pacman -Syu`**

This updates your system and fetches the latest package information.

---

## Step 2: Install Asciiquarium and Its Dependencies

Asciiquarium requires the following dependencies, which are automatically managed by `pacman` during installation:

- **perl**
- **perl-term-animation**


To install Asciiquarium, simply run:

**`sudo pacman -S asciiquarium`**

When prompted, confirm the installation of Asciiquarium and its dependencies.

---

## Step 3: Verify Installation

Once the installation is complete, you can verify that Asciiquarium is working by running:

**`asciiquarium`**

If everything is set up correctly, you’ll see a charming ASCII-art aquarium animate across your terminal screen.

---

## Step 4: Customize Your Experience

Here are a few tips to enhance your Asciiquarium experience:

- **Resize Your Terminal**: Adjust your terminal size to change the display of the aquarium.
- **Exit the Aquarium**: To stop the animation, press **Ctrl+C**.

---


## Conclusion

Installing Asciiquarium on Arch Linux is straightforward thanks to its inclusion in the official repositories. With just a few simple commands, you can bring this quirky and entertaining ASCII aquarium to life on your terminal.

Enjoy the tranquil underwater vibes of Asciiquarium! If you have any questions or fun customizations to share, drop them in the comments below. Happy Linuxing!

