
- Check Exif-data for Authors and such
- Look for PDFs


### Commands

##### DNS
```
nslookup -type=TXT info.megacorptwo.com 192.168.50.151
nslookup -type=TXT megacorpone.com
dnsenum megacorpone.com 
```

##### SMB
```
nmap --script "smb* and not *brute*" -p 139,445 -T4 192.168.187.149
nbtscan -r 192.168.50.0/24           // Scans for computer names, lists the ones with shares.
enum4linux IP                        // Great for finding usernames
// Internal
net view \\dc01 /all                 // CMD command.
```


#### SMTP
Script for testing users
```python
#!/usr/bin/python

import socket
import sys

if len(sys.argv) != 3:
        print("Usage: vrfy.py <username> <target_ip>")
        sys.exit(0)

# Create a Socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Connect to the Server
ip = sys.argv[2]
connect = s.connect((ip,25))

# Receive the banner
banner = s.recv(1024)

print(banner)

# VRFY a user
user = (sys.argv[1]).encode()
s.send(b'VRFY ' + user + b'\r\n')
result = s.recv(1024)

print(result)

# Close the socket
s.close()
```

Internally, either download Telnet or `nc.exe` and run
```
telnet 192.168.50.8 25
nc 192.168.50.8 25
```

### SNMP
The *Simple Network Management Protocol* is not very well-known, and easily misconfigured. It's a UDP-based protocol, vulnerable to IP spoofing and replay attacks.
The encryption scheme SNMP uses is often `DES-56`, which is easily bruteforceable. The newer versions have `AES-256` though.

The SNMP protocol has a database with information, usually about network management. 

| Code                   | Type             |
| ---------------------- | ---------------- |
| 1.3.6.1.2.1.25.1.6.0   | System Processes |
| 1.3.6.1.2.1.25.4.2.1.2 | Running Programs |
| 1.3.6.1.2.1.25.4.2.1.4 | Processes Path   |
| 1.3.6.1.2.1.25.2.3.1.4 | Storage Units    |
| 1.3.6.1.2.1.25.6.3.1.2 | Software Name    |
| 1.3.6.1.4.1.77.1.2.25  | User Accounts    |
| 1.3.6.1.2.1.6.13.1.3   | TCP Local Ports  |
```
// to scan
sudo nmap -sU --open -p 161 192.168.50.0/24 -oN open-snmp.txt
snmpwalk -c public -v1 -t 10 192.168.188.151 -Oa                 // print some general information about the system (some users, the CPU, the physcal network devices)
snmpwalk -c public -v1 192.168.50.151 1.3.6.1.4.1.77.1.2.25      // Enumerate users
snmpwalk -c public -v1 192.168.50.151 1.3.6.1.2.1.25.4.2.1.2     // Enumerate currently running processes
snmpwalk -c public -v1 192.168.50.151 1.3.6.1.2.1.25.6.3.1.2     // Enumerate software
snmpwalk -c public -v1 192.168.50.151 1.3.6.1.2.1.6.13.1.3       // List the current TCP ports
```
