---
layout: page
title: "# Enum Domains and Trusts"
permalink: /ad_cheatsheets/enum_domains_and_trusts
---

# Enum Domains and Trusts

## Enum Domains

```powershell
Get-NetForestDomain
```

## Map Trusts

```powershell
Get-NetDomainTrust
```

## Map all Trusts

```powershell
Get-NetForestDomain | Get-NetDomainTrust
```

## Map Only External Trusts

```powershell
Get-NetForestDomain  | Get-NetDomainTrust | ?{$_.TrustType -eq 'External'}
```

## Extract Info From Forest

```powershell
Get-NetForestDomain -Forest eurocorp.local | Get-NetDomainTrust
```
