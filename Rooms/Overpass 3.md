

Step one: log into FTP, and upload a PHP reverse shell with these creds
```
paradox:ShibesAreGreat123
```

Step two:  escalate to `paradox` with the same credentials.
Add private key to `/home/paradox/.ssh/authorized_keys`


Step three: NFS port forwarded to local?


```
╔══════════╣ Analyzing NFS Exports Files (limit 70)
-rw-r--r--. 1 root root 54 Nov 18  2020 /etc/exports
/home/james *(rw,fsid=0,sync,no_root_squash,insecure)
```

```
╔══════════╣ Active Ports
╚ https://book.hacktricks.xyz/linux-hardening/privilege-escalation#open-ports
tcp     LISTEN   0        64               0.0.0.0:33007          0.0.0.0:*     
tcp     LISTEN   0        128              0.0.0.0:111            0.0.0.0:*     
tcp     LISTEN   0        128              0.0.0.0:20048          0.0.0.0:*     
tcp     LISTEN   0        128              0.0.0.0:22             0.0.0.0:*     
tcp     LISTEN   0        64               0.0.0.0:2049           0.0.0.0:*     
tcp     LISTEN   0        128              0.0.0.0:51013          0.0.0.0:*     
tcp     LISTEN   0        128                 [::]:111               [::]:*     
tcp     LISTEN   0        128                 [::]:20048             [::]:*     
tcp     LISTEN   0        128                    *:80                   *:*     
tcp     LISTEN   0        32                     *:21                   *:*     
tcp     LISTEN   0        128                 [::]:22                [::]:*     
tcp     LISTEN   0        64                  [::]:42241             [::]:*     
tcp     LISTEN   0        64                  [::]:2049              [::]:*     
tcp     LISTEN   0        128                 [::]:59077             [::]:*     


```

Mounted the NFS:

```

sudo mount -v -t nfs localhost:/ /tmp/share_here/  Works :D
```