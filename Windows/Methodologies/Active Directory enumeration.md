

#### Microsoft Management Console
Requirements: RDP access to any AD computer

##### Installation
First, check if it's already installed by pressing `ctrl-r`, then typing `mmc`.
1. Press **Start**
2. Search **"Apps & Features"** and press enter
3. Click **Manage Optional Features**
4. Click **Add a feature**
5. Search for **"RSAT"**
6. Select "**RSAT: Active Directory Domain Services and Lightweight Directory Tools"** and click **Install**

##### Usage
- Click **File** -> **Add/Remove Snap-in**
- Select and **Add** all three Active Directory Snap-ins
- Click through any errors and warnings  

- Right-click on **Active Directory Domains and Trusts** and select **Change Forest**
- Enter _za.tryhackme.com_ as the **Root domain** and Click **OK**
- Right-click on **Active Directory Sites and Services** and select **Change Forest**
- Enter _za.tryhackme.com_ as the **Root domain** and Click OK
- Right-click on **Active Directory Users and Computers** and select **Change Domain**
- Enter _za.tryhackme.com_ as the **Domain** and Click **OK**
- Right-click on **Active Directory Users and Computers** in the left-hand pane  

- Click on **View** -> **Advanced Features**


#### Command prompt
requirements: must be a domain-joined machine
To check if your machine is domain-joined, use `dsregcmd /status`


For enumerating AD, use these commands:
```
net user /domain              // List all domain users
net user USERNAME /domain     // Get information about the domain user

net groups /domain            // List all domain groups
net groups GROUPNAME /domain  // Get information about one of the domain groups

net accounts /domain          // List some basic stuff about the AD (minimum password length, min/max password age, louckout threshold)
```

#### Powershell
For enumerating AD, use these commands:
```powershell
Get-ADDomain -Server SERVER_HOSTNAME                                        // Get some basic information about the AD

Get-ADUser -Identity "IDENTITY_NAME" -Server SERVER_HOSTNAME -Properties *  // Prints all the information about a user. If you are not domain-joined, you need to enter a hostname.
Example:
Get-ADUser -Filter 'Name -like "*stevens"' -Server za.tryhackme.com | Format-Table Name,SamAccountName -A

Get-ADGroup -Identity GROUP_NAME -Server SERVER_HOSTNAME                    // Lists groups. If you are not domain-joined, you need to enter a hostname.
Get-ADGroupMember -Identity GROUP_NAME -Server SERVER_HOSTNAME              // Lists the members of the groups.

// Get all accounts changed after the set ChangeDate.
$ChangeDate = New-Object DateTime(2022, 02, 28, 12, 00, 00)
Get-ADObject -Filter 'whenChanged -gt $ChangeDate' -includeDeletedObjects -Server SERVER_HOSTNAME

Get-ADObject -Filter 'badPwdCount -gt 0' -Server SERVER_HOSTNAME            // Lists the accounts without a password lockout, useful for pass spraying.

// Mofication of the object:
Set-ADAccountPassword -Identity gordon.stevens -Server za.tryhackme.com -OldPassword (ConvertTo-SecureString -AsPlaintext "old" -force) -NewPassword (ConvertTo-SecureString -AsPlainText "new" -Force)
```

#### Bloodhound
There are 2 main parts of bloodhound:
- Bloodhound
- Sharphound
##### Sharphound
Sharphound is the info gathering tool, there are 3 main files:
```
Sharphound.ps1
Sharphound.exe
AzureHound.ps1    // Specifically for Microsoft Azure
```
##### Usage
```
Sharphound.exe --CollectionMethods COLLECTION_METHODS --Domain DOMAIN_HOSTNAME      // To list collection methods, try Sharphound.exe --CollectionMethods
Sharphound.exe --CollectionMethods Default --Domain za.tryhackme.com --ExcludeDCs   // Example
```

##### Bloodhound
To open bloodhound, run
```
neo4j start
bloodhound --no-sandbox
```
The default credentials are `neo4j:neo4j`
