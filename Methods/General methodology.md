

##### Web discovery
- If gobuster doesn't work, use different wordlists  (big.txt, directory-list-2.7-medium.txt, raft-large-directories.txt)
- If you have a hostname, use subdomain fuzzing.
- Check unusual javascript
- Try the defaul password
- Try usuall passwords (admin:password, admin:admin, user:password) etc

##### Privesc
- Run linpeas.sh
- Check mounted and unmounted shares (fdisk)
- Check internal open ports, and set up tunneling (linpeas, chisel)
