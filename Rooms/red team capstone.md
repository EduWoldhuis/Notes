

```
=========|| SERVICE path vulnerable check
Checking for vulnerable service .exe
WinDefend found with permissions issue:
Identity BUILTIN\Users BUILTIN\Users has 'Write'
serviceName found with permissions issue:
Identity Everyone has 'FullControl' perms for C:\Backup Service\Full Backup\backup.exe
serviceName found with permissions issue:
Identity BUILTIN\Users has 'FullControl' perms for C:\Backup Service\Full Backup\backup.exe
WdNisSvc found with permissions issue:
Identity BUILTIN\Users BUILTIN\Users has 'Write' perms for C:\ProgramData

=========|| Checking for Unquoted Service Paths
Fetching the list of services, this may take a while...
Unquoted Service Path found!
Name: Backup
PathName: C:\Backup Service\Full Backup\backup.exe
StartName: LocalSystem
StartMode: Manual
Running: Stopped
Unquoted Service Path found!
Name: serviceName
PathName: C:\Backup Service\Full Backup\backup.exe
StartName: LocalSystem
StartMode: Manual
Running: Stopped                            
```

```
C:\Backup Service\Full Backup
```