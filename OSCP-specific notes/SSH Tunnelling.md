

### Local port forwarding

``` 
ssh -N -L 0.0.0.0:4455:172.16.217.217:445 database_admin@10.4.217.215           // on 192.168.217.63
// Open port 4455 on 192.168.217.63, sending the packets through 172.16.217.217 to 10.4.217.215:445.
```
`-N`: spawn no shell, give no output.



### Dynamic port forwarding
When dynamic port forwarding, all ports on the target machine are forwarded. To achieve this, a SOCKS5 proxy to a specific IP is used:

```
ssh -N -D 0.0.0.0:9999 USERNAME@HIDDEN_MACHINE           // Run from the remote machine
// change "socks5 127.0.0.1 1080" to "socks5 REMOTE_IP 9999" in /etc/proxychains.conf locally
proxychains nmap -n -sT --top-port=10 HIDDEN_MACHINE_REACHABLE_IP        // make sure to use '-n' and '-sT'
```


```
confluence@confluence01:/opt/atlassian/confluence/bin$ ssh -N -D 0.0.0.0:9999 database_admin@10.4.217.215
Could not create directory '/home/confluence/.ssh'.
The authenticity of host '10.4.217.215 (10.4.217.215)' can't be established.
ECDSA key fingerprint is SHA256:GMUxFQSTWYtQRwUc9UvG2+8toeDPtRv3sjPyMfmrOH4.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Failed to add the host to the list of known hosts (/home/confluence/.ssh/known_hosts).
database_admin@10.4.217.215's password: 


┌─[root@edu-virtualbox]─[/tmp/oscp]
└──╼ #tail /etc/proxychains.conf 
#        ( auth types supported: "basic"-http  "user/pass"-socks )
#
[ProxyList]
# add proxy here ...
# meanwile
# defaults set to "tor"
#socks5 127.0.0.1 5000
#socks5  127.0.0.1 1080
socks5  192.168.217.63 9999
// sets the target machine (running the port forwarder) and IP.

┌─[✗]─[root@edu-virtualbox]─[/tmp/oscp]
└──╼ #proxychains nmap -sT -n --top-port=10 -Pn 172.16.217.217
ProxyChains-3.1 (http://proxychains.sf.net)
Starting Nmap 7.94 ( https://nmap.org ) at 2024-05-06 00:57 CEST
RTTVAR has grown to over 2.3 seconds, decreasing to 2.0
Host is up (10s latency).

PORT     STATE  SERVICE
21/tcp   closed ftp
22/tcp   closed ssh
23/tcp   closed telnet
25/tcp   closed smtp
80/tcp   closed http
110/tcp  closed pop3
139/tcp  open   netbios-ssn
443/tcp  closed https
445/tcp  open   microsoft-ds
3389/tcp open   ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 105.67 seconds


```