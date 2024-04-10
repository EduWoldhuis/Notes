
OSCP Note: use the `best64` or `rockyou-30000` rule:
```
john --list=rules                                                               // display all rules
john --rules=best64 --wordlist=/usr/share/wordlists/rockyou.txt hash.txt        // use best64 rule
// Don't use rockyou-30000 with john, too inefficient.


hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force               // NTLM
hashcat -m 13400 hash.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/rockyou-30000.rule --force       // Keepass
./hashcat.exe -m 13400 /tmp/hash /usr/share/wordlists/rockyou.txt -r rules/rockyou-30000.rule --force                   // Keepass on WSL for GPU access
./hashcat.exe -m 22921 ssh.hash ssh.passwords -r ssh.rule --force                                                       // With rule (only works on some SSH)
./hashcat.exe -m 5600 /tmp/hash /usr/share/wordlists/rockyou.txt -r rules/best64.rule                                   // Net-NTLMv2
```

Custom John rules:
```
/usr/share/john/john-local.conf
```

Common hashes:
https://hashcat.net/wiki/doku.php?id=example_hashes

| Hash       | Hashcat code              | Example |
| ---------- | ------------------------- | ------- |
| MD5        | 0                         |         |
| SHA1       | 100                       |         |
| NTLM       | 1000                      |         |
| LM         | 3000                      |         |
| SHA256     | 1400                      |         |
| SHA512     | 1700                      |         |
| Kerberos   | 18200 (may be other ones) |         |
| Keepass    | 13400                     |         |
| Net-NTLMv2 | 5600                      |         |
