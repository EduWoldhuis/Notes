
Types of MSF exploits:


| Type      | Explanation                                                                      |
| --------- | -------------------------------------------------------------------------------- |
| Auxiliary | Auxiliary modules don't exploit, but they can scan, modify, crack passwords etc. |
| Exploit   | Get RCE                                                                          |
| Payload   | Create payloads for other modules                                                |
|           |                                                                                  |
Example search: 
```
search type:auxiliary ssh
```

##### Workspaces
```
msfdb init             // Initialize the MSF database

workspace -a pen-200   // create a workspace
workspace pen-200      // Attach to a workspace
worspace -d pen-200    // Delete a workspace
```


To get the IP address and ports into the MSF datbase, use:
```
db_nmap -A IP_ADDR
hosts // will display the IP handled before
services // will display the services the NMAP scan foud.
services -p 8000
```

### payloads
See [[msfvenom]].
To show payloads:
```
show payloads
use X
```
##### Staged vs non-staged payloads
Note the /
```
Staged:
payload/linux/x64/meterpreter/bind_tcp                             normal  No     Linux Mettle x64, Bind TCP Stager
payload/linux/x64/meterpreter/reverse_sctp                         normal  No     Linux Mettle x64, Reverse SCTP Stager
payload/linux/x64/meterpreter/reverse_tcp                          normal  No     Linux Mettle x64, Reverse TCP Stager

Non-staged:
payload/linux/x64/meterpreter_reverse_http                         normal  No     Linux Meterpreter, Reverse HTTP Inline
payload/linux/x64/meterpreter_reverse_https                        normal  No     Linux Meterpreter, Reverse HTTPS Inline
payload/linux/x64/meterpreter_reverse_tcp                          normal  No     Linux Meterpreter, Reverse TCP Inline
```

Note: make sure to use this for `exploit/multi/handler`

