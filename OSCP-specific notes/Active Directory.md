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


### SPN
