
In Windows, nearly all event logging is done by **ETW** (Event Tracing for Windows).
The general method these events are logged are:
```
Device --> Event collector/forwarder --> Data Indexer --> SIEM Server
```

ETW is made up of three main parts:

| Component | Function | Exploit techniques |
| ---- | ---- | ---- |
| Provider |  | PSEtwLogProvider Modification, Group Policy Takeover, Log Pipeline Abuse, Type Creation |
| Controller |  | Patching EtwEventWrite, Runtime Tracing Tampering, |
| Consumers |  | Log Smashing, Log Tampering |



#### Aggresive approach - log smashing
Generally, Windows logs can be removed or destroyed. For this approach to work when red teaming, they need to be destroyed before they are (potentially) forwarded to a SIEM server.
The big drawback to this approach is that the action removing logs will generate logs, meaning the SOC team will be able to see the logs were tampered with.
##### Created events
| ID | event |
| ---- | ---- |
| 1102 | The Windows Security Audit log was cleared |
| 104 | The time when the log file was cleared |
| 1100 | The time when the Windows Event Log service was shut down |

