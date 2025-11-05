# UDP 2

#### Super easy to use "scapy" but wanted to do it in Python. Could not use Pythons socket library, had to import scapy and then craft the same scapy packet in more steps:

```python
from scapy.all import IP, UDP, sr1, Raw

src_ip = "10.0.0.1"
sport = 31338
dst_ip = "10.0.0.2"
dport = 31337
msg = "Hello, World!\n"

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
