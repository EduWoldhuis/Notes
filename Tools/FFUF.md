##### Vulnerable web parameter scan:
```
ffuf -u 'http://IP/DOCUMENT/?FUZZ=1' -c -w /usr/share/seclists/Discovery/Web-Content/raft-medium-words-lowercase.txt -fs 0
ffuf -u 'http://IP/DOCUMENT/?FUZZ=1' -c -w /usr/share/seclists/Discovery/Web-Content/raft-medium-words-lowercase.txt -fw 39 -o ffuf/param_scan.txt
```

##### Fuzz vhost: (with IP.PARAM)
```
ffuf -u http://IP.PARAM -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H 'Host: FUZZ.IP.PARAM' -fs 0	 
ffuf -u http://IP.PARAM -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H 'Host: FUZZ.IP.PARAM' -fs 0 -o ffuf/vhost_scan.txt
```
note: replace 0 in `-fs 0`  with the filesize to exclude
note: there can be multiple levels of subdomain (`abc.def.ghi.safe.com`)

##### Fuzz with parameter (usernames and such)
```ffuf -w WORDLIST -X POST -d "username=FUZZ" -H "Content-Type: application/x-www-form-urlencoded" -u http://IP/ -mr "username already exists"```
##### Proxy
```-x http://127.0.0.1:PORT```

##### Multiple wordlists: (usernames,passwords)
```
ffuf -w WORDLIST1:FUZZ1,WORDLIST2:FUZZ2 // FUZZ paramaters are FUZZ1, FUZZ2
```
