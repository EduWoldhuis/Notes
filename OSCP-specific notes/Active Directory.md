```
Get-NetUser | select cn,pwdlastset,lastlogon
Get-NetGroup | select cn,description
Get-NetGroup "Sales Department" | select member


Get-NetComputer
Get-NetComputer | select operatingsystem,dnshostname

```

#### Step 1: users and groups
```
Get-NetUser | select cn,pwdlastset,lastlogon      //
Get-NetGroup | select cn                          // Common name 
Get-NetGroup "GroupName" | select member
```


#### Step 3 other machines
```
Find-LocalAdminAccess                            // Finds computers on the network where the current user has Admin access, from Powerview
Get-NetComputer | Select name                    // From PowerView
.\PsLoggedOn.exe \\COMPUTER_NAME                 // Finds where other users are/have logged on, from SysInternals. Requires the "Remote Registry" service to be enabled on the target, if not, use "Get-NetSession -ComputerName NAME"

```


### Domain shares
```
Find-DomainShare                                 // From PowerView, enumerated AD domain shares, might take 1-5 minutes
Find-DomainShare -CheckShareAccess               // Only display available shares, might take 1-5 minutes
ls \\dc1.corp.com\sysvol\corp.com\               // Example on connecting via Powershell
```
Note: if there are any readable Policy files, there will be an `AES-256`-encrypted password. To decrypt it, use `gpp-decrypt`


### SPN (Service Principle Name)
SPNs are the unique identifiers of Service Accounts. Using them, we can find out what IPs and ports a service uses.
```
Get-NetUser -SPN | select samaccountname,serviceprincipalname            // From powerview.ps1
setspn.exe -L SAMACCOUNTNAME
setspn.exe -L USERNAME
```
To get the IP, use
```
nslookup.exe web04.corp.com
```


### ACL / ACE
The Access Controll List (ACL) and Access Control Entries (ACE) decide user-specific accesses to AD objects. 
```
GenericAll: Full permissions on object
GenericWrite: Edit certain attributes on the object
WriteOwner: Change ownership of the object
WriteDACL: Edit ACE's applied to object
AllExtendedRights: Change password, reset password, etc.
ForceChangePassword: Password change for object
Self (Self-Membership): Add ourselves to for example a group
```

To list:
```
// Permissions of users in a group (not the group itself) to find targets
Get-ObjectAcl -Identity "Management Department" | ? {$_.ActiveDirectoryRights -eq "GenericAll"} | select SecurityIdentifier,ActiveDirectoryRights  
```
