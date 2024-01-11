
### Window Event Viewer
#### Filtering by specific sources in Windows Event Viewer
#### Filtering by:
First, enter `Create Custom View` or `Filter Current Custom View`

| Filter | Location |
| ---- | ---- |
| Log source type (specific applications) | By source --> find the application |
| Event types (critical / warning ) etc | Event level |
| Users / computers | Users / computers |
| Custom log ranges | Logs --> custom range |
| Event IDs | set `<all event IDs>` to only the target ID. |
Note: Powershell logs are in two different places:
```
Microsoft-Windows-PowerShell/Admin,Microsoft-Windows-PowerShell/Operational
Windows PowerShell
```



### Wevtutil.exe
wevtutil is a CLI tool for filtering through a large amount of Event Logs


#### Useful commands
```
wevtutil.exe /?                                 -- Get help
wevtutil.exe COMMAND /?                         -- Get specific help
wevtutil.exe qe Application /rd:false /f:text   -- Print all the events, in text format and read first event first.
```

### Powershell (`Get-WinEvent`)
https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.diagnostics/get-winevent?view=powershell-7.4

#### Useful commands
```
Get-Help Get-WinEvent                           -- Get help
Get-WinEvent -ListLog *                         -- List the log sources (Application, security, Microsoft-Windows-Windows Defender/Operational etc...)
wevtutil.exe qe Application /rd:false /f:text   -- Print all the events, in text format and read first event first.
```
##### General filtering

```
(Get-WinEvent -ListProvider Microsoft-Windows-PowerShell).Events |Format-Table Id, Description // List powershell events

Get-WinEvent -LogName Application | Where-Object { $_.ProviderName -Match 'WLMS' }         // Get only logs from "WLMS".

// Get only Application logs from WLMS
Get-WinEvent -FilterHashtable @{LogName='Application';ProviderName='WLMS'}
// Guidelines: @{ <name> = <value>; [<name> = <value> ] ...}

# Using the FilterHashtable parameter:
$Yesterday = (Get-Date) - (New-TimeSpan -Day 1)
Get-WinEvent -FilterHashtable @{ LogName='Windows PowerShell'; Level=3; StartTime=$Yesterday }
```

To get the possible names and values, use the Event Viewer:
- Find a target packet (or a similar packet) in the Event Viewer.
- In the "general information" tab in the middle, the possible names and values are listed (Log name: Application  ;  Source: MsiInstalled  ;  Event ID: 11707)
- List these values into the hash table.


#### Xpath queries
```
Get-WinEvent -LogName Application -FilerXPath '*/System/EventID=100'   // List every log where the Event ID is 100

Get-WinEvent -LogName Application -FilerXPath '*[System[(Level <= 3) and TimeCreated[timediff(@SystemTime) <= 86400000]]]' // All events where the log level is equal to or lower than 3, and happened in the last 24 hours.

```

#### Combination with Wevtutil.exe
```
wevtutil.exe qe Application /q:*/System[EventID=100] /f:text /c:1
```
### General useful list of IDs


| ID | Action |
| ---- | ---- |
| 4104 | Execute a Remote Command |
| 800 | Details on commands ran  |
|  |  |
