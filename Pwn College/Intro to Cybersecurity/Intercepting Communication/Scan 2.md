# Scan 2

#### Nmap and then netcat the ip:
```
nmap -sn -n -v -T5 --min-rate 1000 10.0.0.0/16 | grep -B 1 "Host is up"
```
