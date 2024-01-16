
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

