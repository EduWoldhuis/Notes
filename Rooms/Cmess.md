
The domain was `cmess.thm`, so I used subdomain enumeration.
The sizes were all different, so I used the FFUF `-fw` word filter.
`dev.cmess.thm` showed up.

Admin login page found at `cmess.thm/admin`

Added a PHP reverse shell to the `index.php` page

Found Andre's backup password:
```
www-data@cmess:/tmp$ cat /opt/.password.bak 
andres backup password
UQfsdCB7aAP6
```


##### Credentials
Admin panel: `andre@cmess.thm : KPFTN_f2yxe%`
Internal user: `andre : UQfsdCB7aAP6`
##### Found users
-- dev.cmess.thm:
andre
support

