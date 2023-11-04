##### Quick directory scan:
	`gobuster dir -w /usr/share/wordlists/dirb/big.txt -u IP`
	`gobuster dir -w /usr/share/wordlists/dirb/big.txt -u IP > web/gobuster_scan.txt`

##### Common extensions scan:
	`gobuster dir -w /usr/share/wordlists/dirb/big.txt -u IP -x .html,.php,.txt`
	`gobuster dir -w /usr/share/wordlists/dirb/big.txt -u IP -x .html,.php,.txt > web/gobuster_extensions_scan.txt`

##### Size filter scan:
	`gobuster dir -w /usr/share/wordlists/dirb/big.txt -u IP --exclude-length SIZE`

##### Add cookie:
`-c "COOKIE"`