# A Basic cIMG (C)

#### Run ```gdb /challenge/cimg```, set a break at main, run, disassemble. Find the length of the header which is 18 bytes:
```asm
0x00000000004012f8 <+148>:   mov    $0x12,%edx
```
--------------------------------------

#### Magic number is next, this makes up 4 bytes, index 0-3:
```asm
   0x0000000000401302 <+158>:   cmpb   $0x63,0x6(%rsp)
   0x0000000000401307 <+163>:   jne    0x40131e <main+186>
   0x0000000000401309 <+165>:   cmpb   $0x49,0x7(%rsp)
   0x000000000040130e <+170>:   jne    0x40131e <main+186>
   0x0000000000401310 <+172>:   cmpb   $0x4d,0x8(%rsp)
   0x0000000000401315 <+177>:   jne    0x40131e <main+186>
   0x0000000000401317 <+179>:   cmpb   $0x47,0x9(%rsp)
   0x000000000040131c <+184>:   je     0x401332 <main+206>
```
----------------------------------------

#### The version is at <+206> and is 2, looking for 4 bytes so '<I'. The width is at <+220> and is 73(0x49 hex), looking for 8 bytes so '<Q'. Last is height at <+235> and is 20(0x14 hex), looking for 2 bytes so '<H'.
```asm
   0x0000000000401332 <+206>:   cmpl   $0x2,0xa(%rsp)
   0x0000000000401337 <+211>:   lea    0xe12(%rip),%rdi        # 0x402150
   0x000000000040133e <+218>:   jne    0x401325 <main+193>
   0x0000000000401340 <+220>:   cmpq   $0x49,0xe(%rsp)
   0x0000000000401346 <+226>:   lea    0xe1f(%rip),%rdi        # 0x40216c
   0x000000000040134d <+233>:   jne    0x401325 <main+193>
   0x000000000040134f <+235>:   cmpw   $0x14,0x16(%rsp)
```
----------------------------------------------

#### Here's the color requirements:
```asm
   0x00000000004013f7 <+403>:   cmpb   $0x8c,(%rbx,%rax,4)
   0x00000000004013fb <+407>:   jne    0x40140e <main+426>
   0x00000000004013fd <+409>:   cmpb   $0x1d,0x1(%rbx,%rax,4)
   0x0000000000401402 <+414>:   jne    0x40140e <main+426>
   0x0000000000401404 <+416>:   cmpb   $0x40,0x2(%rbx,%rax,4)
```
-----------------------------------------------

#### Part of the pixel loop check and the total length needed at <+456>, which is 1460:
```asm
   0x000000000040141e <+442>:   cmpb   $0x20,0x3(%rbx,%rcx,4)
   0x0000000000401423 <+447>:   je     0x401427 <main+451>
   0x0000000000401425 <+449>:   inc    %esi
   0x0000000000401427 <+451>:   inc    %rcx
   0x000000000040142a <+454>:   jmp    0x401419 <main+437>
   0x000000000040142c <+456>:   cmp    $0x5b4,%esi
```
------------------------------------------------

#### Setup the script and submit:
```python
import struct

w = 73
h = 20


magic = b"cIMG"                
version = struct.pack('<I', 2)
width = struct.pack('<Q', w)
height = struct.pack('<H', h) 

total = w * h

r = b'\x8c'
g = b'\x1d'
b = b'\x40'
char = b'S'
pixel = r + g + b + char
data = pixel * total

cimg_base = magic + version + width + height

cimg = cimg_base + data

filename = "/home/hacker/s.cimg"
with open(filename, "wb") as f:
    f.write(cimg)
```
