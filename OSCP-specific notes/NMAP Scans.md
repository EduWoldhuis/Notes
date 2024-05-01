
```
nmap -sC -script smb-vuln*.nse -p 139,445 -oN smb-vuln-scan IP
```

### Adding custom .NSE scripts
After finding a NSE script, copy it to the `nmap` scripts directory:

```
cp FILENAME.nse /usr/share/nmap/scripts/
```
Then, update the `nmap` Scripts database:
```
nmap --script-updatedb
```
Then, to use it:
```
nmap --script "FILENAME"
```