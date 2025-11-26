# Behold the cIMG! (C)

#### Run gdb and disass:
```asm
Dump of assembler code for function main:
=> 0x0000000000401284 <+0>:     endbr64
   0x0000000000401288 <+4>:     push   %r12
   0x000000000040128a <+6>:     mov    %edi,%r8d
   0x000000000040128d <+9>:     mov    $0xf,%ecx
   0x0000000000401292 <+14>:    push   %rbp
   0x0000000000401293 <+15>:    push   %rbx
   0x0000000000401294 <+16>:    sub    $0x20,%rsp
   0x0000000000401298 <+20>:    mov    %fs:0x28,%rax
   0x00000000004012a1 <+29>:    mov    %rax,0x18(%rsp)
   0x00000000004012a6 <+34>:    xor    %eax,%eax
   0x00000000004012a8 <+36>:    lea    0x9(%rsp),%rdi
--Type <RET> for more, q to quit, c to continue without paging--c
   0x00000000004012ad <+41>:    dec    %r8d
   0x00000000004012b0 <+44>:    lea    0x9(%rsp),%rbp
   0x00000000004012b5 <+49>:    rep stos %al,%es:(%rdi)
   0x00000000004012b7 <+51>:    jle    0x401308 <main+132>
   0x00000000004012b9 <+53>:    mov    0x8(%rsi),%r12
   0x00000000004012bd <+57>:    or     $0xffffffffffffffff,%rcx
   0x00000000004012c1 <+61>:    lea    0xe0b(%rip),%rsi        # 0x4020d3
   0x00000000004012c8 <+68>:    mov    %r12,%rdi
   0x00000000004012cb <+71>:    repnz scas %es:(%rdi),%al
   0x00000000004012cd <+73>:    not    %rcx
   0x00000000004012d0 <+76>:    lea    -0x6(%r12,%rcx,1),%rdi
   0x00000000004012d5 <+81>:    call   0x4011d0 <strcmp@plt>
   0x00000000004012da <+86>:    test   %eax,%eax
   0x00000000004012dc <+88>:    je     0x4012f3 <main+111>
   0x00000000004012de <+90>:    lea    0xdf4(%rip),%rsi        # 0x4020d9
   0x00000000004012e5 <+97>:    mov    $0x1,%edi
   0x00000000004012ea <+102>:   xor    %eax,%eax
   0x00000000004012ec <+104>:   call   0x4011f0 <__printf_chk@plt>
   0x00000000004012f1 <+109>:   jmp    0x40134a <main+198>
   0x00000000004012f3 <+111>:   xor    %esi,%esi
   0x00000000004012f5 <+113>:   mov    %r12,%rdi
   0x00000000004012f8 <+116>:   xor    %eax,%eax
   0x00000000004012fa <+118>:   call   0x401210 <open@plt>
   0x00000000004012ff <+123>:   xor    %esi,%esi
   0x0000000000401301 <+125>:   mov    %eax,%edi
   0x0000000000401303 <+127>:   call   0x401190 <dup2@plt>
   0x0000000000401308 <+132>:   or     $0xffffffff,%r8d
   0x000000000040130c <+136>:   xor    %edi,%edi
   0x000000000040130e <+138>:   lea    0xde3(%rip),%rcx        # 0x4020f8
   0x0000000000401315 <+145>:   mov    %rbp,%rsi
   0x0000000000401318 <+148>:   mov    $0xf,%edx
   0x000000000040131d <+153>:   call   0x40161b <read_exact>
   0x0000000000401322 <+158>:   cmpb   $0x63,0x9(%rsp)
   0x0000000000401327 <+163>:   jne    0x40133e <main+186>
   0x0000000000401329 <+165>:   cmpb   $0x49,0xa(%rsp)
   0x000000000040132e <+170>:   jne    0x40133e <main+186>
   0x0000000000401330 <+172>:   cmpb   $0x4d,0xb(%rsp)
   0x0000000000401335 <+177>:   jne    0x40133e <main+186>
   0x0000000000401337 <+179>:   cmpb   $0x47,0xc(%rsp)
   0x000000000040133c <+184>:   je     0x401352 <main+206>
   0x000000000040133e <+186>:   lea    0xdd1(%rip),%rdi        # 0x402116
   0x0000000000401345 <+193>:   call   0x401160 <puts@plt>
   0x000000000040134a <+198>:   or     $0xffffffff,%edi
   0x000000000040134d <+201>:   call   0x401220 <exit@plt>
   0x0000000000401352 <+206>:   cmpb   $0x1,0xd(%rsp)
   0x0000000000401357 <+211>:   lea    0xdd5(%rip),%rdi        # 0x402133
   0x000000000040135e <+218>:   jne    0x401345 <main+193>
   0x0000000000401360 <+220>:   movzwl 0x16(%rsp),%r12d
   0x0000000000401366 <+226>:   imul   0xe(%rsp),%r12
   0x000000000040136c <+232>:   mov    %r12,%rdi
   0x000000000040136f <+235>:   call   0x4011e0 <malloc@plt>
   0x0000000000401374 <+240>:   lea    0xdd4(%rip),%rdi        # 0x40214f
   0x000000000040137b <+247>:   mov    %rax,%rbx
   0x000000000040137e <+250>:   test   %rax,%rax
   0x0000000000401381 <+253>:   je     0x401345 <main+193>
   0x0000000000401383 <+255>:   mov    %r12d,%edx
   0x0000000000401386 <+258>:   mov    %rax,%rsi
   0x0000000000401389 <+261>:   or     $0xffffffff,%r8d
   0x000000000040138d <+265>:   xor    %edi,%edi
   0x000000000040138f <+267>:   lea    0xdee(%rip),%rcx        # 0x402184
   0x0000000000401396 <+274>:   call   0x40161b <read_exact>
   0x000000000040139b <+279>:   movzwl 0x16(%rsp),%edx
   0x00000000004013a0 <+284>:   imul   0xe(%rsp),%rdx
   0x00000000004013a6 <+290>:   xor    %eax,%eax
   0x00000000004013a8 <+292>:   cmp    %rax,%rdx
   0x00000000004013ab <+295>:   je     0x4013dc <main+344>
   0x00000000004013ad <+297>:   movzbl (%rbx,%rax,1),%ecx
   0x00000000004013b1 <+301>:   inc    %rax
   0x00000000004013b4 <+304>:   lea    -0x20(%rcx),%esi
   0x00000000004013b7 <+307>:   cmp    $0x5e,%sil
   0x00000000004013bb <+311>:   jbe    0x4013a8 <main+292>
   0x00000000004013bd <+313>:   mov    0x2c7c(%rip),%rdi        # 0x404040 <stderr@@GLIBC_2.2.5>
   0x00000000004013c4 <+320>:   lea    0xdd5(%rip),%rdx        # 0x4021a0
   0x00000000004013cb <+327>:   mov    $0x1,%esi
   0x00000000004013d0 <+332>:   xor    %eax,%eax
   0x00000000004013d2 <+334>:   call   0x401230 <__fprintf_chk@plt>
   0x00000000004013d7 <+339>:   jmp    0x40134a <main+198>
   0x00000000004013dc <+344>:   mov    %rbx,%rsi
   0x00000000004013df <+347>:   mov    %rbp,%rdi
   0x00000000004013e2 <+350>:   call   0x40166b <display>
   0x00000000004013e7 <+355>:   movzwl 0x16(%rsp),%ecx
   0x00000000004013ec <+360>:   xor    %eax,%eax
   0x00000000004013ee <+362>:   xor    %edx,%edx
   0x00000000004013f0 <+364>:   imul   0xe(%rsp),%rcx
   0x00000000004013f6 <+370>:   cmp    %rax,%rcx
   0x00000000004013f9 <+373>:   je     0x401408 <main+388>
   0x00000000004013fb <+375>:   cmpb   $0x20,(%rbx,%rax,1)
   0x00000000004013ff <+379>:   je     0x401403 <main+383>
   0x0000000000401401 <+381>:   inc    %edx
   0x0000000000401403 <+383>:   inc    %rax
   0x0000000000401406 <+386>:   jmp    0x4013f6 <main+370>
   0x0000000000401408 <+388>:   cmp    $0x113,%edx
   0x000000000040140e <+394>:   jne    0x401417 <main+403>
   0x0000000000401410 <+396>:   xor    %eax,%eax
   0x0000000000401412 <+398>:   call   0x401526 <win>
   0x0000000000401417 <+403>:   mov    0x18(%rsp),%rax
   0x000000000040141c <+408>:   xor    %fs:0x28,%rax
   0x0000000000401425 <+417>:   je     0x40142c <main+424>
   0x0000000000401427 <+419>:   call   0x401180 <__stack_chk_fail@plt>
   0x000000000040142c <+424>:   add    $0x20,%rsp
   0x0000000000401430 <+428>:   xor    %eax,%eax
   0x0000000000401432 <+430>:   pop    %rbx
   0x0000000000401433 <+431>:   pop    %rbp
   0x0000000000401434 <+432>:   pop    %r12
   0x0000000000401436 <+434>:   ret
End of assembler dump.
```
----------------------------------------------

#### The length of the header is 15 bytes, shown here: 
```asm
<+148>:   mov    $0xf,%edx
```
------------------------------------------------

#### Here is the magic number:
```asm
0x0000000000401322 <+158>:   cmpb   $0x63,0x9(%rsp)
   0x0000000000401327 <+163>:   jne    0x40133e <main+186>
   0x0000000000401329 <+165>:   cmpb   $0x49,0xa(%rsp)
   0x000000000040132e <+170>:   jne    0x40133e <main+186>
   0x0000000000401330 <+172>:   cmpb   $0x4d,0xb(%rsp)
   0x0000000000401335 <+177>:   jne    0x40133e <main+186>
   0x0000000000401337 <+179>:   cmpb   $0x47,0xc(%rsp)
```
-----------------------------------------------

#### Here is the version, width and height. Can see the version is 4 bytes, width is 2 bytes and height is 1 byte. Total is 113 (275 decimal):
```asm
0x0000000000401352 <+206>: cmpl $0x1,0x11(%rsp)
0x0000000000401360 <+220>: movzwl 0x15(%rsp),%r12d
0x0000000000401366 <+226>: movzbl 0x17(%rsp),%edx
0x000000000040140d <+393>: cmp $0x113,%edx
```
------------------------------------------------

#### This is the check loop for the image. For every non space character the counter increases, and if it equals 275 the win condition is called, otherwise it skips:
```asm
0x0000000000401400 <+380>: cmpb $0x20,(%rbx,%rax,1)
0x0000000000401404 <+384>: je 0x401408 <main+388>
0x0000000000401406 <+386>: inc %edx
0x0000000000401408 <+388>: inc %rax
0x000000000040140b <+391>: jmp 0x4013fc <main+376>
0x000000000040140d <+393>: cmp $0x113,%edx
0x0000000000401413 <+399>: jne 0x40141c <main+408>
```

#### Create the struct and package with the data and submit. Have to pick a combo of values for width and height that make 275, tried 25 and 11:

```python
import struct

w = 25
h = 11


magic = b"cIMG"                
version = struct.pack('<I', 1)
width = struct.pack('<H', w)
height = struct.pack('<B', h) 

data = w * h
padding = b'F' * data

cimg_base = magic + version + width + height

cimg = cimg_base + padding

filename = "/home/hacker/s.cimg"
with open(filename, "wb") as f:
    f.write(cimg)
```
