https://juggernaut-sec.com/password-hunting/


### Active directory
ADs store a lot of information, make sure to check:
- Description of users: `Get-ADUser -Filter * -Properties * | select Name,SamAccountName,Description`
- Group policy on SYSVOL (version XXX is vulnerable)
- NTDS (contains credentials, check if available)

### Cleartext
Cleartext credentials can sometimes be found in:
Powershell history: 
```
type C:\Users\USER\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt              // Will also be flagged by Winpeas (PowerShell settings tab)
```
Backup files: 
```
Get-Childitem –Path C:\ -Include *.bak -File -Recurse -ErrorAction SilentlyContinue
```
`Winpeas` log (under "Interesting Files")

### Password managers
`Winpeas` will scan for existing password managers

### Using mimikatz
[[Mimikatz]]




