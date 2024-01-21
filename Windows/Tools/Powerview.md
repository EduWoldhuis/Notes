##### Requirements
Powershell on the target system

##### Description
Powerview is a Powershell information gathering tool for Windows.

##### Installation
```
wget https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1
Import-Module .\powerview.ps1
```


##### Usage

```
Get-NetDomainController   // Output information on the domain controller.
Get-NetGroup "Domain Admins" // Output information on the domain administrators
Find-DomainShare // List all domain shares
	Get-NetComputer | select name  // List all computers on the AD
```