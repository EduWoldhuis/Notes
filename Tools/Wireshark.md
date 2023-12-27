
### Name resolution (IP address to hostname)
```
edit --> preferences --> Name resolution --> Resolve transport names, resolve netwrork address
```

### HTTPS decryption
Example file:
``` 
CLIENT_HANDSHAKE_TRAFFIC_SECRET f35fff3ca92d297cb03a5f158d65286c7afdd583e6392a84b13a9f7ac431a222 f4ce2a326ffa83c7c1f1fc00e142471438547a951cd6e383f6492a0aa386fcf8
SERVER_HANDSHAKE_TRAFFIC_SECRET f35fff3ca92d297cb03a5f158d65286c7afdd583e6392a84b13a9f7ac431a222 7ebf1f8a0cbe86f2605b4cf95692e87eff41a109bf640817f543fedb8f8f0d04
CLIENT_HANDSHAKE_TRAFFIC_SECRET 56fe125f02c940b756b92628f605a330585ef28f780514d680ed88523be33091 6a3f63a5da6de0a09470383723582aee56e4b1bd483f54ffbd5fa5f66a044c21
SERVER_HANDSHAKE_TRAFFIC_SECRET 56fe125f02c940b756b92628f605a330585ef28f780514d680ed88523be33091 2c4d244b1878e3da8311c429dacfbd3baf68f0a76fd37dff43e4a88f5c3be804
CLIENT_TRAFFIC_SECRET_0 56fe125f02c940b756b92628f605a330585ef28f780514d680ed88523be33091 160fd713d014210e4513754b50f515fec5574a2eef712e8c303b6759d622e7ec
SERVER_TRAFFIC_SECRET_0 56fe125f02c940b756b92628f605a330585ef28f780514d680ed88523be33091 82675967e543cb88da9acb4a589b6e5abe159b676a48aa5fcdd8dd6662fae4de
EXPORTER_SECRET 56fe125f02c940b756b92628f605a330585ef28f780514d680ed88523be33091 72f13bd4d127b9b6dbefcd9914ff35b20fac66c77cd13a5e9ec1c2cf4bd738aa
// Can be hundreds or thousands of lines long.
```
This is a logfile of SSL keys, created by a browser with the setting enabled.
To decrypt HTTPS using it:
```
Edit --> Preferences --> Protocols --> TLS --> (Pre)-Master-Secret log filename --> enter the file.
```
### Extracting plaintext credentials
Wireshark can extract usernames and passwords from protocols like `HTTP` with `BASIC-AUTH` and plain `FTP`.
To view them, check
```
tools --> Credentials
```
