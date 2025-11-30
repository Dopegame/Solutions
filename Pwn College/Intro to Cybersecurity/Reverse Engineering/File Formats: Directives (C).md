# File Formats: Directives (C)

#### Same as the others to start, gdb, disassemble:
```asm
(gdb) disas main
Dump of assembler code for function main:
   0x00000000004012a4 <+0>:     endbr64
   0x00000000004012a8 <+4>:     push   %r15
   0x00000000004012aa <+6>:     xorps  %xmm0,%xmm0
   0x00000000004012ad <+9>:     push   %r14
   0x00000000004012af <+11>:    push   %r13
   0x00000000004012b1 <+13>:    push   %r12
   0x00000000004012b3 <+15>:    push   %rbp
   0x00000000004012b4 <+16>:    push   %rbx
   0x00000000004012b5 <+17>:    sub    $0x38,%rsp
   0x00000000004012b9 <+21>:    mov    %fs:0x28,%rax
   0x00000000004012c2 <+30>:    mov    %rax,0x28(%rsp)
   0x00000000004012c7 <+35>:    xor    %eax,%eax
   0x00000000004012c9 <+37>:    dec    %edi
   0x00000000004012cb <+39>:    movups %xmm0,0x10(%rsp)
   0x00000000004012d0 <+44>:    movq   $0x0,0x20(%rsp)
   0x00000000004012d9 <+53>:    jle    0x40132a <main+134>
   0x00000000004012db <+55>:    mov    0x8(%rsi),%rbp
   0x00000000004012df <+59>:    or     $0xffffffffffffffff,%rcx
   0x00000000004012e3 <+63>:    lea    0xed8(%rip),%rsi        # 0x4021c2
   0x00000000004012ea <+70>:    mov    %rbp,%rdi
   0x00000000004012ed <+73>:    repnz scas %es:(%rdi),%al
   0x00000000004012ef <+75>:    not    %rcx
--Type <RET> for more, q to quit, c to continue without paging--c
   0x00000000004012f2 <+78>:    lea    -0x6(%rbp,%rcx,1),%rdi
   0x00000000004012f7 <+83>:    call   0x4011f0 <strcmp@plt>
   0x00000000004012fc <+88>:    test   %eax,%eax
   0x00000000004012fe <+90>:    je     0x401315 <main+113>
   0x0000000000401300 <+92>:    lea    0xec1(%rip),%rsi        # 0x4021c8
   0x0000000000401307 <+99>:    mov    $0x1,%edi
   0x000000000040130c <+104>:   xor    %eax,%eax
   0x000000000040130e <+106>:   call   0x401210 <__printf_chk@plt>
   0x0000000000401313 <+111>:   jmp    0x40135f <main+187>
   0x0000000000401315 <+113>:   xor    %esi,%esi
   0x0000000000401317 <+115>:   mov    %rbp,%rdi
   0x000000000040131a <+118>:   xor    %eax,%eax
   0x000000000040131c <+120>:   call   0x401230 <open@plt>
   0x0000000000401321 <+125>:   xor    %esi,%esi
   0x0000000000401323 <+127>:   mov    %eax,%edi
   0x0000000000401325 <+129>:   call   0x4011a0 <dup2@plt>
   0x000000000040132a <+134>:   lea    0x10(%rsp),%rbp
   0x000000000040132f <+139>:   or     $0xffffffff,%r8d
   0x0000000000401333 <+143>:   xor    %edi,%edi
   0x0000000000401335 <+145>:   mov    $0xc,%edx
   0x000000000040133a <+150>:   lea    0xea6(%rip),%rcx        # 0x4021e7
   0x0000000000401341 <+157>:   mov    %rbp,%rsi
   0x0000000000401344 <+160>:   call   0x40165b <read_exact>
   0x0000000000401349 <+165>:   cmpl   $0x474d4963,0x10(%rsp)
   0x0000000000401351 <+173>:   je     0x401367 <main+195>
   0x0000000000401353 <+175>:   lea    0xeab(%rip),%rdi        # 0x402205
   0x000000000040135a <+182>:   call   0x401170 <puts@plt>
   0x000000000040135f <+187>:   or     $0xffffffff,%edi
   0x0000000000401362 <+190>:   call   0x401240 <exit@plt>
   0x0000000000401367 <+195>:   cmpw   $0x3,0x14(%rsp)
   0x000000000040136d <+201>:   lea    0xeae(%rip),%rdi        # 0x402222
   0x0000000000401374 <+208>:   jne    0x40135a <main+182>
   0x0000000000401376 <+210>:   mov    %rbp,%rdi
   0x0000000000401379 <+213>:   lea    0xe(%rsp),%rbx
   0x000000000040137e <+218>:   call   0x40188e <initialize_framebuffer>
   0x0000000000401383 <+223>:   mov    0x18(%rsp),%eax
   0x0000000000401387 <+227>:   lea    -0x1(%rax),%edx
   0x000000000040138a <+230>:   mov    %edx,0x18(%rsp)
   0x000000000040138e <+234>:   test   %eax,%eax
   0x0000000000401390 <+236>:   je     0x4013de <main+314>
   0x0000000000401392 <+238>:   lea    0xea5(%rip),%rcx        # 0x40223e
   0x0000000000401399 <+245>:   or     $0xffffffff,%r8d
   0x000000000040139d <+249>:   mov    %rbx,%rsi
   0x00000000004013a0 <+252>:   xor    %edi,%edi
   0x00000000004013a2 <+254>:   mov    $0x2,%edx
   0x00000000004013a7 <+259>:   call   0x40165b <read_exact>
   0x00000000004013ac <+264>:   movzwl 0xe(%rsp),%ecx
   0x00000000004013b1 <+269>:   cmp    $0x4ec9,%cx
   0x00000000004013b6 <+274>:   jne    0x4013c2 <main+286>
   0x00000000004013b8 <+276>:   mov    %rbp,%rdi
   0x00000000004013bb <+279>:   call   0x4016ab <handle_20169>
   0x00000000004013c0 <+284>:   jmp    0x401383 <main+223>
   0x00000000004013c2 <+286>:   mov    0x6dd7(%rip),%rdi        # 0x4081a0 <stderr@@GLIBC_2.2.5>
   0x00000000004013c9 <+293>:   lea    0xe95(%rip),%rdx        # 0x402265
   0x00000000004013d0 <+300>:   mov    $0x1,%esi
   0x00000000004013d5 <+305>:   xor    %eax,%eax
   0x00000000004013d7 <+307>:   call   0x401250 <__fprintf_chk@plt>
   0x00000000004013dc <+312>:   jmp    0x40135f <main+187>
   0x00000000004013de <+314>:   xor    %esi,%esi
   0x00000000004013e0 <+316>:   mov    %rbp,%rdi
   0x00000000004013e3 <+319>:   lea    0x2c36(%rip),%r12        # 0x404020 <desired_output>
   0x00000000004013ea <+326>:   xor    %ebx,%ebx
   0x00000000004013ec <+328>:   call   0x40182d <display>
   0x00000000004013f1 <+333>:   mov    0x1c(%rsp),%r14d
   0x00000000004013f6 <+338>:   mov    0x20(%rsp),%r13
   0x00000000004013fb <+343>:   cmp    $0x2b8,%r14d
   0x0000000000401402 <+350>:   sete   %bl
   0x0000000000401405 <+353>:   xor    %ebp,%ebp
   0x0000000000401407 <+355>:   xor    %r15d,%r15d
   0x000000000040140a <+358>:   cmp    %r14d,%ebp
   0x000000000040140d <+361>:   jae    0x40144e <main+426>
   0x000000000040140f <+363>:   cmp    $0x2b8,%ebp
   0x0000000000401415 <+369>:   je     0x40144e <main+426>
   0x0000000000401417 <+371>:   mov    0x13(%r13),%al
   0x000000000040141b <+375>:   cmp    0x13(%r12),%al
   0x0000000000401420 <+380>:   cmovne %r15d,%ebx
   0x0000000000401424 <+384>:   cmp    $0x20,%al
   0x0000000000401426 <+386>:   je     0x401442 <main+414>
   0x0000000000401428 <+388>:   cmp    $0xa,%al
   0x000000000040142a <+390>:   je     0x401442 <main+414>
   0x000000000040142c <+392>:   mov    $0x18,%edx
   0x0000000000401431 <+397>:   mov    %r12,%rsi
   0x0000000000401434 <+400>:   mov    %r13,%rdi
   0x0000000000401437 <+403>:   call   0x4011e0 <memcmp@plt>
   0x000000000040143c <+408>:   test   %eax,%eax
   0x000000000040143e <+410>:   cmovne %r15d,%ebx
   0x0000000000401442 <+414>:   inc    %ebp
   0x0000000000401444 <+416>:   add    $0x18,%r13
   0x0000000000401448 <+420>:   add    $0x18,%r12
   0x000000000040144c <+424>:   jmp    0x40140a <main+358>
   0x000000000040144e <+426>:   test   %ebx,%ebx
   0x0000000000401450 <+428>:   je     0x401459 <main+437>
   0x0000000000401452 <+430>:   xor    %eax,%eax
   0x0000000000401454 <+432>:   call   0x401566 <win>
   0x0000000000401459 <+437>:   mov    0x28(%rsp),%rax
   0x000000000040145e <+442>:   xor    %fs:0x28,%rax
   0x0000000000401467 <+451>:   je     0x40146e <main+458>
   0x0000000000401469 <+453>:   call   0x401190 <__stack_chk_fail@plt>
   0x000000000040146e <+458>:   add    $0x38,%rsp
   0x0000000000401472 <+462>:   xor    %eax,%eax
   0x0000000000401474 <+464>:   pop    %rbx
   0x0000000000401475 <+465>:   pop    %rbp
   0x0000000000401476 <+466>:   pop    %r12
   0x0000000000401478 <+468>:   pop    %r13
   0x000000000040147a <+470>:   pop    %r14
   0x000000000040147c <+472>:   pop    %r15
   0x000000000040147e <+474>:   ret
End of assembler dump.
```
------------------------------------------------------

#### Head is now 12 bytes. Same cIMG header beginning. Version is now 3. It also added a directive_code. Total pixels is 696. 
```asm
0x0000000000401335 <+145>:   mov    $0xc,%edx
0x0000000000401349 <+165>:   cmpl   $0x474d4963,0x10(%rsp)
0x0000000000401367 <+195>:   cmpw   $0x3,0x14(%rsp)
0x00000000004013b1 <+269>:   cmp    $0x4ec9,%cx
0x000000000040140f <+363>:   cmp    $0x2b8,%ebp
```
-----------------------------------------------------

#### Fixed the width and height loop and simplified the struct sequence:
```python
from pwn import *
import os
import struct
import re

context.log_level = 'error'
io = process(['gdb', '/challenge/cimg'])

io.sendline(b'b main')
io.recvuntil(b'(gdb)')
io.sendline(b'r')
io.recvuntil(b'(gdb)')
io.sendline(b'disas')
io.recvuntil(b'(gdb)')
io.sendline(b'c')
io.recvuntil(b'(gdb)')

size = 16704
start = 0x404020
end = hex(int(start) + size)
file = '/tmp/dump.bin'

disas_cimg = (f'dump binary memory {file} {start} {end}')
io.sendline(disas_cimg.encode())
io.recvuntil(b'(gdb)')
io.sendline(b'q')
io.sendline(b'y')

with open(file, 'rb') as f:
        desired_cimg = f.read()
if len(desired_cimg) == size:
    print("Size is correct.")

pixel_data = 16704
pixel_size = 24
pixel_list = []
pixel_rgba_search = re.compile(rb'\x1b\[38;2;(?P<r>\d{1,3});(?P<g>\d{1,3});(?P<b>\d{1,3})m(?P<a>.)\x1b\[0m', re.DOTALL)

for i in range(0, pixel_data, pixel_size):
	pixel_block = desired_cimg[i : i + pixel_size]
	pixel_rgba = pixel_rgba_search.match(pixel_block)
	r = int(pixel_rgba.group('r').decode())
	g = int(pixel_rgba.group('g').decode())
	b = int(pixel_rgba.group('b').decode())
	a = ord(pixel_rgba.group('a').decode('latin1'))
	build_pixel = struct.pack('<BBBB', r, g, b, a)
	pixel_list.append(build_pixel)

pixels = b''.join(pixel_list)

total_pixels = 696
max_dimension = 255

for width in range(1, max_dimension + 1):
    if total_pixels % width == 0:
        height = total_pixels // width
        
        if height <= max_dimension:
            package = struct.pack('<IHBBLH', 0x474d4963, 3, width, height, 1, 20169)

            with open("s.cimg", "wb") as f:
                    f.write(package + pixels)

            io.close()
            io = process(['/challenge/cimg', 's.cimg'])
            out = io.recvall(timeout=2)
            flag_found = re.search(rb'pwn\.college\{.*?\}', out)
            if flag_found:
                flag = flag_found.group(0).decode()
                print(f"Found flag with width {width} and height {height}.")
                print(flag)
                exit()
```
