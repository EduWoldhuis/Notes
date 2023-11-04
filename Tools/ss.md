ss (socket statistics) is a better version of netstat
```
ss -tulpn // Show TCP, UDP sockets, only listening, with running process, without service names.
ss -tulp  // Previous command, but show naming (for extra context).

```