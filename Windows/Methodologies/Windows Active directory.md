```
systeminfo | findstr Domain   // Active directory check

Get-ADUser  -Filter *   // Get info about AD

Get-MpComputerStatus | select RealTimeProtectionEnabled   // Check if AV is running

Get-MpComputerStatus   // Get all AV information

Get-NetFirewallProfile | Format-Table Name, Enabled   // Get firewall profile

Get-NetFirewallRule | select DisplayName, Enabled, Description   // Get firewall rules (tons of output)

Get-MpThreat   // View previously blocked threats


```