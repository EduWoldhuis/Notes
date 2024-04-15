```
Get-NetUser | select cn,pwdlastset,lastlogon
Get-NetGroup | select cn
Get-NetGroup "Sales Department" | select member


Get-NetComputer
Get-NetComputer | select operatingsystem,dnshostname

```

#### Step 1: users
```
Get-NetUser | select cn,pwdlastset,lastlogon      //
Get-NetGroup | select cn                          // Common name 
Get-NetGroup "GroupName" | select member
```
