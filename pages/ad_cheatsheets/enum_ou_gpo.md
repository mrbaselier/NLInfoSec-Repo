---
layout: page
title: "# Enum OU'S & GPO's"
permalink: /ad_cheatsheets/enum_ou_gpo
---

# Enum OU'S & GPO's

## Enum GPO

```powershell
Get-NetGPOGroup -Verbose
```

## Membership Of RDPUsers

```powershell
Get-NetGroupMember -GroupName RDPUsers
```

## List All OU's

```powershell
Get-NetOU
```

## List All computers To OU

```powershell
Get-NetOU StudentMachines | %{Get-NetComputer -ADSpath $_}
```

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/219ca035-23da-4625-b63e-98ffba1648db/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/219ca035-23da-4625-b63e-98ffba1648db/Untitled.png)

## List GPO

```powershell
Get-NetGPO
```

## Enum GPO Applied To OU

```powershell
Get-NetGPO -ADSpath 'LDAP://cn={3E04167E-C2B6-4A9A-8FB7-C811158DC97C},cn=policies,cn=system,DC=dollarcorp,DC=moneycorp,DC=local'
```

## Load AD Module:

[Active Directory Module.zip](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bb7acd29-ce41-4df9-94ca-5c69ab2c3d23/Active_Directory_Module.zip)

Or use: [https://github.com/samratashok/ADModule?files=1](https://github.com/samratashok/ADModule?files=1)

## Other Resources

Grouper2:

[https://github.com/l0ss/Grouper2](https://github.com/l0ss/Grouper2)

[Local Group Enumeration](http://www.harmj0y.net/blog/redteaming/local-group-enumeration/)
