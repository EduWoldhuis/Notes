
OSCP Note: use the `best64` or `rockyou-30000` rule:
```
john --list=rules                                                               // display all rules
john --rules=best64 --wordlist=/usr/share/wordlists/rockyou.txt hash.txt        // use best64 rule
// Don't use rockyou-30000 with john, too inefficient.


hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force               // NTLM
hashcat -m 13400 hash.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/rockyou-30000.rule --force       // Keepass
./hashcat.exe -m 13400 /tmp/hash /usr/share/wordlists/rockyou.txt -r rules/rockyou-30000.rule --force                   // Keepass on WSL for GPU access
./hashcat.exe -m 22921 ssh.hash ssh.passwords -r ssh.rule --force                                                       // With rule (only works on some SSH)
```

Custom John rules:
```
/usr/share/john/john-local.conf
```

Common hashes:
https://hashcat.net/wiki/doku.php?id=example_hashes

| Hash     | Hashcat code              | Example                                                                                                                                                                                                                                                                                                                 |
| -------- | ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| MD5      | 0                         |                                                                                                                                                                                                                                                                                                                         |
| SHA1     | 100                       |                                                                                                                                                                                                                                                                                                                         |
| NTLM     | 1000                      |                                                                                                                                                                                                                                                                                                                         |
| LM       | 3000                      |                                                                                                                                                                                                                                                                                                                         |
| SHA256   | 1400                      |                                                                                                                                                                                                                                                                                                                         |
| SHA512   | 1700                      |                                                                                                                                                                                                                                                                                                                         |
| Kerberos | 18200 (may be other ones) |                                                                                                                                                                                                                                                                                                                         |
| Keepass  | 13400                     | `$keepass$*2*60*0*d74e29a727e9338717d27a7d457ba3486d20dec73a9db1a7fbc7a068c9aec6bd*04b0bfd787898d8dcd4d463ee768e55337ff001ddfac98c961219d942fb0cfba*5273cc73b9584fbd843d1ee309d2ba47*1dcad0a3e50f684510c5ab14e1eecbb63671acae14a77eff9aa319b63d71ddb9*17c3ebc9c4c3535689cb9cb501284203b7c66b0ae2fbf0c2763ee920277496c1` |
