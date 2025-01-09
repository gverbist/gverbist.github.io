+++
title = "Unclutter on wayland"
date = "2025-01-09T07:45:20+01:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Bist"
authorTwitter = "" #do not include @
cover = ""
tags = ["sway", "linux"]
keywords = ["linux", ""]
description = "Unclutter works for X11, for wayland you need to do it another way."
showFullContent = false
readingTime = false
hideComments = false
+++
# Unclutter for Wayland
---
Unclutter, which is commonly used to hide the mouse cursor on X11, does not work under Wayland. However, there are alternatives available for Wayland environments.

For GNOME on Wayland, you can use the “Hide Cursor” GNOME extension, which is available via the GNOME “Extension Manager.” This extension allows you to hide the mouse pointer after a period of inactivity.

Additionally, for other Wayland compositors, such as Sway, you can add the following lines to your configuration file to hide the cursor after a period of mouse inactivity:

```seat seat0 hide_cursor 3000```

The number “3000” refers to the number of milliseconds, which translates to 3 seconds in this case. This method is similar to what unclutter would do on an X11 environment.

Enjoy a clutter free desktop
