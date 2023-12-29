### General tunnelling
To tunnel, use SSH port tunneling:
```
ssh -L LOCAL_PORT:INSIDE_TUNNEL_IP_ADDRES:REMOTE_PORT USER@IP_ADDRESS
```

For the `INSIDE_TUNNEL_IP_ADDRESS`, use the IP address you want to connect to for access (usually localhost)
Note: the `INSIDE_TUNNEL_IP_ADDRESS` is the host that the remote SSH service will use as `host:` parameter.  It's the target from the remote point of view.


### Open a local port to the public:
```
ssh -R 8181:localhost:3389 TARGET_PORT
// "gateway ports" and "allowtcpforwarding" needs to be set to "yes" in the sshdconfig
```


### Socks proxy
```
ssh -D INPUT_PORT user@IP_ADDRESS
```
Make sure to use `FoxyProxy` to the `INPUT_PORT`.
### Non-SSH tools
For general tunnelling / pivoting, use [[Chisel]] and [[Socat]]
