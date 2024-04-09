
OSCP Note: use the `best64` or `rockyou-30000` rule:

```
john --list=rules                                                               // display all rules
john --rules=best64 --wordlist=/usr/share/wordlists/rockyou.txt hash.txt        // use best64 rule

hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
```

Common hashes:
https://hashcat.net/wiki/doku.php?id=example_hashes

| Hash     | Hashcat code              |
| -------- | ------------------------- |
| MD5      | 0                         |
| SHA1     | 100                       |
| NTLM     | 1000                      |
| LM       | 3000                      |
| SHA256   | 1400                      |
| SHA512   | 1700                      |
| Kerberos | 18200 (may be other ones) |
