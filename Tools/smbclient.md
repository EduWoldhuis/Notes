
```C++
smbclient -L \\\\IP\\  // Lists the shares
smbclient \\\\IP\\SHARE  // Connects anonymously to a share
smbclient -U DOMAIN/USER \\\\IP\\SHARE PASSWORD // Connects to a share with a user account
```