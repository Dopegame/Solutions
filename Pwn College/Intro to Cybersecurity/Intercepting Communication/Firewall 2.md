# Firewall 2

#### Exclude 10.0.0.3:
```iptables -A INPUT -s 10.0.0.3 -p tcp -j DROP```
