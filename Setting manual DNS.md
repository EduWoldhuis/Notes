For manual DNS entries, enter it as the first `nameserver`:
```
┌─[root@edu-virtualbox]─[/home/edu/THM/lateral_movement_and_pivoting]
└──╼ #cat /etc/resolv.conf 
# Generated by NetworkManager
search 10.200.91.101
nameserver 10.200.91.101   # Tryhackme box
nameserver 192.168.1.97    # Actual DNS
```
