
Types of MSF exploits:


| Type      | Explanation                                                                      |
| --------- | -------------------------------------------------------------------------------- |
| Auxiliary | Auxiliary modules don't exploit, but they can scan, modify, crack passwords etc. |
| Exploit   |                                                                                  |
| Payload   |                                                                                  |
|           |                                                                                  |



To get the IP address and ports into the MSF datbase, use:
```
db_nmap -A IP_ADDR
hosts // will display the IP handled before
services // will display the services the NMAP scan foud.
services -p 8000
```

