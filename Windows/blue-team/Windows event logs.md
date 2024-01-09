
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

### General useful list of IDs


| ID | Action |
| ---- | ---- |
| 4104 | Execute a Remote Command |
| 800 | Details on commands ran  |
|  |  |
