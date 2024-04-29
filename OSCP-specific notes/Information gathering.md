
- Check Exif-data for Authors and such
- Look for PDFs


### Commands

##### DNS
```
nslookup -type=TXT info.megacorptwo.com 192.168.50.151
nslookup -type=TXT megacorpone.com
dnsenum megacorpone.com 
```

##### SMB
```
sudo nbtscan -r 192.168.50.0/24           // Scans for computer names
nmap -v -p 139,445 --script smb-os-discovery 192.168.50.152

// Internal
net view \\dc01 /all                      // CMD command.
```