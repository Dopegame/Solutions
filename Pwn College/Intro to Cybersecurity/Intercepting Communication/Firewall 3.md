# Firewall 3

#### Open up 31337 for traffic and then netcat 10.0.0.2:
```iptables -I OUTPUT -p tcp -d 10.0.0.2 --dport 31337 -j ACCEPT```

```nc 10.0.0.2 31337```
