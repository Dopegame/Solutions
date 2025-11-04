# Monitor 1

#### Just have to piece the flag together after finding it:
```tshark -i 1 -V -f "port 31337" | grep -A 5 "pwn.college{"```
