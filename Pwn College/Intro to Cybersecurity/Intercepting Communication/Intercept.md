# Intercept

#### First find the MAC address:

<img width="500" height="300" alt="Screenshot by Snip My on Nov 5, 2025 at 3 49 09 PM" src="https://github.com/user-attachments/assets/002592f8-0155-4106-bde9-a6e6937b2f3d" />

-------------------------------

#### Then find the MAC address of 10.0.0.2 and send a packet to get the intercept info:

<img width="500" height="300" alt="Screenshot by Snip My on Nov 5, 2025 at 3 52 33 PM" src="https://github.com/user-attachments/assets/ee26c879-57d0-4352-9f28-aa106916f131" />

<img width="500" height="199" alt="Screenshot by Snip My on Nov 5, 2025 at 3 59 48 PM" src="https://github.com/user-attachments/assets/83393d49-74ef-474a-823b-1d54f9777ffd" />

------------------------------

#### Next have to send off a fake request:

<img width="1932" height="115" alt="Screenshot by Snip My on Nov 5, 2025 at 4 05 40 PM" src="https://github.com/user-attachments/assets/4ad6437d-fdce-4789-aecf-51dc6ed9fa7a" />

----------------------------------------

#### Then add 10.0.0.3 to receive traffic:

```ip addr add 10.0.0.3/24 dev eth0```

----------------------------------------

#### Then finally open a server to listen, also figured out a simpler way to listen via Python, however, netcat has always been more reliable. Sometimes python just doesn't work for whatever reason:

```python
import socket

s = socket.socket()
s.bind(("10.0.0.3", 31337))
s.listen(1)

print("Searching...")
conn, _ = s.accept()
flag = conn.recv(1024).decode()
print(f"{flag}")
```




