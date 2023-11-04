To tunnel, use SSH port tunneling:
```
ssh -L LOCAL_PORT:LOCAL_IP_ADDRES:REMOTE_PORT USER@IP_ADDRESS
```
For the `LOCAL_IP_ADDRESS`, use the IP address you want to connect to for access (usually localhost)


To mount:
```
sudo mount -v -t nfs localhost:/ /tmp/share_here/
```
