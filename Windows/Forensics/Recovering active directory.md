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

Some important `Event Viewer` IDs to look for:
- ID **4756**: Member added to a universal security group.
- ID **4757**: Member removed from a universal security group.
- ID **4728**: Member added to a global security group.
- ID **4729**: Member removed from a global security group.

### Post-compromise - Domain takeback

##### Recovery plan
- Reset all `tier 0` (regular users) account passwords. Tool: `Active Directory Users and Computers`
- Look for possibly compromised or suspicious accounts, and disable or remove them. Tool: `Active Directory Users and Computers`
- Reset the passwords of any accounts with admin privileges. Tool:   `Active Directory Users and Computers`
- Use the `Reset-ComputerMachinePassword` Powershell command to reset passwords of computer objects on the domain.
- Reset the password of the DC, to prevent a [Silver ticket](Golden ticket#Silver tickets) attack.  Tool: `Active Directory Users and Computers`
- If there is a safe backup of the DC, restore it.
- Perform malware analysis on any attacked DC or user.
- Make sure there are no scheduled tasks. Tool: `run --> schtasks.msc`
- Check event logs, Access Control Lists (ACL), and group policies for any changes.
- Enable traffic filtering on inbound traffic, to identify Indicators of Compromise (IOC). This is a task for the security team (SOC).

### Common misconfigurations
##### BIOS boot order
If the boot order / allowance is improperly set in the BIOS, an attacker can plug in another SSD, USB or disks to boot from, giving access to all other SSDs and their data.
##### AD Sever Administrator Group members
By default, any AD user (member of `Domain Users` group), can log into any workstation in the AD. This is usually a massive vulnerability, so there are a few steps that can be taken to limit user access.
Tool: `run --> gpedit.msc`
In the group policy editor, navigate to:
```
Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > User Rights Assignment
```
Here, double-click on the `Allow log on locally` option, and select only the users or groups who should be able to log into the DC.
##### Weak passwords
Make sure there are password rules and regulations set on the AD, otherwise a lot of users can be compromised by bruteforce, or passwords can be saved post-compromise.
##### DCSync attacks
If the attacker compromises any account or group with `Replicating Directory Changes` and `Replicating Directory Changes All` rights, a DCSync attack can be performed.
A DCSync attack uses the `GetNCChanges` Windows command, cloning an entire AD Domain. This includes all users and their password hashes, plus all data. At this point, everything is compromised.
To prevent this, make sure to minimize any users and groups with the `Replicating Directory Changes` and `Replicating Directory Changes All` priveleges.
##### Scripts and Applications Permissions on Workstations
If a domain user can run unauthorised scripts or commands, it's trivial for them to enumerate the entire domain and to run exploits. To prevent this or make it more difficult, set a restriction policy on scripts and applications.
##### Configure Network Time Synchronisation
It's a lot easier to correlate log source data from different AD domains / servers if their time is propery synchronised. 
