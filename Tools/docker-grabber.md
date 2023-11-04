##### Requirements
A found docker registry port (default 5000)

##### Usage
```
dockergrabber.py http://IP --list           // List docker images on the IP with the default port.
dockergrabber.py http://IP --dump IMAGE     // Dump docker image to current directory (get IMAGE from --list)
dockergrabber.py http://ip --dump_all       // Dump all docker images to current directory
```