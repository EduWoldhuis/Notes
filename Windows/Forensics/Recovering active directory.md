
### Immediate action - first response
-  create a backup of the compromised AD server using `run --> wbadmin.msc`. This backup can be used later on for advanced analysis, and to prevent more damage from being done.
-  restore the Trusted Backup of the server. Some data will be lost (AD objects like users, computers etc), but they can be restored from the previously made compromised backup.
-  Segregate the network to prevent pivoting and communication with the attacker, and activate a secondary domain controller to keep the services up and running to the users.
-  Enable advanced firewall monitoring features, filter traffic, and record all packets to try to find the attacker.
-  Limit the creation of AD objects and GPOs, if possible.

### Forensics - how did it happen
Use the [[Windows event logs]]  in the `Event Viewer` to view the events leading up to the compromise, and use `Bloodhound` and `PowerView` to identify the vulnerabilities.

### Forensics - locating any infection vectors

##### Detecting user creation / modifications
List all the users created (and enabled) after the date of the compromise.
```powershell
Get-ADUser -Filter {((Enabled -eq $True) -and (Created -gt "Monday, April 10, 2023 00:00:00 AM"))} -Property Created, LastLogonDate | select SamAccountName, Name, Created | Sort-Object Created
// Modified to list logins after compromise date
Get-ADUser -Filter {((Enabled -eq $True) -and (LastLogonDate -gt "December 1, 1999 00:00:00 AM"))} -Property Created, LastLogonDate | select SamAccountName, Name, Created | Sort-Object Created
```
Note: when looking into users, use the Windows `Active Directory Users and Computers` tool for more information.
##### Detecting Domain-joined computers
```powershell
Get-ADComputer -filter * -properties whencreated | Select Name,@{n="Owner";e={(Get-acl "ad:\$($_.distinguishedname)").owner}},whencreated 
```

- ID **4756**: Member added to a universal security group.
- ID **4757**: Member removed from a universal security group.
- ID **4728**: Member added to a global security group.
- ID **4729**: Member removed from a global security group.

