+++
title = "Rubrik PowerShell API Connection"
date = "2025-06-25T08:55:14+02:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Bist"
cover = "rubrik.png"
description = "Connect securely to a Rubrik Security Cloud (RSC) environment using PowerShell."
showFullContent = false
readingTime = false
hideComments = true
+++


Rubrik's powerful APIs unlock immense automation and integration possibilities. 
## Goal of this guide

- Connect securely to a Rubrik Security Cloud (RSC) environment using PowerShell.

---

## What is an API (and PowerShell Cmdlets)?

At its core, an **API (Application Programming Interface)** is like a menu in a restaurant. It defines a set of rules and protocols for how different software applications can communicate with each other.

**PowerShell Cmdlets** are like simplified order slips. The `RubrikSecurityCloud` PowerShell module provides these cmdlets, abstracting away much of the API complexity.

---

## Installing the powershell module

```powershell
Install-Module -Name RubrikSecurityCloud -Force

Import-Module -Name RubrikSecurityCloud

Get-Module -ListAvailable -Name RubrikSecurityCloud

Get-Command -Module RubrikSecurityCloud
```

### 3. Establishing a Secure Connection to Rubrik Security Cloud (RSC)

#### a. Create a Service Account in RSC

1. Go to **Settings → Users & Access → Service Accounts**
2. Click **ADD SERVICE ACCOUNT**
3. Name it and assign the **Administrator** role
4. Click **DOWNLOAD AS JSON**

#### b. Encrypt Your Service Account File

```powershell
Set-RscServiceAccountFile -InputFilePath C:\Users\Administrator\Downloads\power_shell_lab_account.json `
  -OutputFilePath C:\Users\Administrator\Documents\rsc_encrypted_account.bin
```

#### c. Connect to RSC

```powershell
Connect-Rsc -ServiceAccountFile C:\Users\Administrator\Documents\rsc_encrypted_account.bin
```

#### d. Verify Your Connection

```powershell
Get-RscCluster
```

---

This setup confirms your secure connection and enables API-powered PowerShell workflows for Rubrik.

