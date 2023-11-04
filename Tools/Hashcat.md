[Hashes list](https://hashcat.net/wiki/doku.php?id=example_hashes) (for -m flag)

##### Crack hash:
`hashcat -a 0 -m HASHNUM hash.txt /usr/share/wordlists/rockyou.txt`

##### Show result:
`hashcat --show hash.txt`