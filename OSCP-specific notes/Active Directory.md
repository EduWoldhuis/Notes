```
Get-NetUser | select cn,pwdlastset,lastlogon
Get-NetGroup | select cn
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