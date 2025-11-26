# Internal State Mini (C)

#### First run gdb ```gdb /challenge/cimg``` and set a break at main. Run and disassemble:
```asm
(gdb) b main
Breakpoint 1 at 0x4012a4
(gdb) run
Starting program: /challenge/cimg 

Breakpoint 1, 0x00000000004012a4 in main ()
(gdb) disass main
Dump of assembler code for function main:
=> 0x00000000004012a4 <+0>:     endbr64
   0x00000000004012a8 <+4>:     push   %r15
   0x00000000004012aa <+6>:     xorps  %xmm0,%xmm0
   0x00000000004012ad <+9>:     push   %r14
   0x00000000004012af <+11>:    push   %r13
   0x00000000004012b1 <+13>:    push   %r12
   0x00000000004012b3 <+15>:    push   %rbp
   0x00000000004012b4 <+16>:    push   %rbx
   0x00000000004012b5 <+17>:    sub    $0x28,%rsp
   0x00000000004012b9 <+21>:    mov    %fs:0x28,%rax
   0x00000000004012c2 <+30>:    mov    %rax,0x18(%rsp)
   0x00000000004012c7 <+35>:    xor    %eax,%eax
   0x00000000004012c9 <+37>:    dec    %edi
--Type <RET> for more, q to quit, c to continue without paging--c
   0x00000000004012cb <+39>:    movups %xmm0,(%rsp)
   0x00000000004012cf <+43>:    movq   $0x0,0x10(%rsp)
   0x00000000004012d8 <+52>:    jle    0x401329 <main+133>
   0x00000000004012da <+54>:    mov    0x8(%rsi),%rbp
   0x00000000004012de <+58>:    or     $0xffffffffffffffff,%rcx
   0x00000000004012e2 <+62>:    lea    0xe56(%rip),%rsi        # 0x40213f
   0x00000000004012e9 <+69>:    mov    %rbp,%rdi
   0x00000000004012ec <+72>:    repnz scas %es:(%rdi),%al
   0x00000000004012ee <+74>:    not    %rcx
   0x00000000004012f1 <+77>:    lea    -0x6(%rbp,%rcx,1),%rdi
   0x00000000004012f6 <+82>:    call   0x4011f0 <strcmp@plt>
   0x00000000004012fb <+87>:    test   %eax,%eax
   0x00000000004012fd <+89>:    je     0x401314 <main+112>
   0x00000000004012ff <+91>:    lea    0xe3f(%rip),%rsi        # 0x402145
   0x0000000000401306 <+98>:    mov    $0x1,%edi
   0x000000000040130b <+103>:   xor    %eax,%eax
   0x000000000040130d <+105>:   call   0x401210 <__printf_chk@plt>
   0x0000000000401312 <+110>:   jmp    0x40135b <main+183>
   0x0000000000401314 <+112>:   xor    %esi,%esi
   0x0000000000401316 <+114>:   mov    %rbp,%rdi
   0x0000000000401319 <+117>:   xor    %eax,%eax
   0x000000000040131b <+119>:   call   0x401230 <open@plt>
   0x0000000000401320 <+124>:   xor    %esi,%esi
   0x0000000000401322 <+126>:   mov    %eax,%edi
   0x0000000000401324 <+128>:   call   0x4011a0 <dup2@plt>
   0x0000000000401329 <+133>:   mov    %rsp,%r12
   0x000000000040132c <+136>:   or     $0xffffffff,%r8d
   0x0000000000401330 <+140>:   xor    %edi,%edi
   0x0000000000401332 <+142>:   mov    $0x8,%edx
   0x0000000000401337 <+147>:   lea    0xe26(%rip),%rcx        # 0x402164
   0x000000000040133e <+154>:   mov    %r12,%rsi
   0x0000000000401341 <+157>:   call   0x40167b <read_exact>
   0x0000000000401346 <+162>:   cmpl   $0x474d4963,(%rsp)
   0x000000000040134d <+169>:   je     0x401363 <main+191>
   0x000000000040134f <+171>:   lea    0xe2c(%rip),%rdi        # 0x402182
   0x0000000000401356 <+178>:   call   0x401170 <puts@plt>
   0x000000000040135b <+183>:   or     $0xffffffff,%edi
   0x000000000040135e <+186>:   call   0x401240 <exit@plt>
   0x0000000000401363 <+191>:   cmpw   $0x2,0x4(%rsp)
   0x0000000000401369 <+197>:   lea    0xe2f(%rip),%rdi        # 0x40219f
   0x0000000000401370 <+204>:   jne    0x401356 <main+178>
   0x0000000000401372 <+206>:   mov    %r12,%rdi
   0x0000000000401375 <+209>:   call   0x401816 <initialize_framebuffer>
   0x000000000040137a <+214>:   movzbl 0x6(%rsp),%ebx
   0x000000000040137f <+219>:   movzbl 0x7(%rsp),%edx
   0x0000000000401384 <+224>:   imul   %edx,%ebx
   0x0000000000401387 <+227>:   movslq %ebx,%rbx
   0x000000000040138a <+230>:   shl    $0x2,%rbx
   0x000000000040138e <+234>:   mov    %rbx,%rdi
   0x0000000000401391 <+237>:   call   0x401200 <malloc@plt>
   0x0000000000401396 <+242>:   lea    0xe1e(%rip),%rdi        # 0x4021bb
   0x000000000040139d <+249>:   mov    %rax,%rbp
   0x00000000004013a0 <+252>:   test   %rax,%rax
   0x00000000004013a3 <+255>:   je     0x401356 <main+178>
   0x00000000004013a5 <+257>:   mov    %ebx,%edx
   0x00000000004013a7 <+259>:   mov    %rax,%rsi
   0x00000000004013aa <+262>:   or     $0xffffffff,%r8d
   0x00000000004013ae <+266>:   xor    %edi,%edi
   0x00000000004013b0 <+268>:   lea    0xe39(%rip),%rcx        # 0x4021f0
   0x00000000004013b7 <+275>:   call   0x40167b <read_exact>
   0x00000000004013bc <+280>:   movzbl 0x7(%rsp),%eax
   0x00000000004013c1 <+285>:   movzbl 0x6(%rsp),%edx
   0x00000000004013c6 <+290>:   imul   %eax,%edx
   0x00000000004013c9 <+293>:   xor    %eax,%eax
   0x00000000004013cb <+295>:   cmp    %eax,%edx
   0x00000000004013cd <+297>:   jle    0x4013ff <main+347>
   0x00000000004013cf <+299>:   movzbl 0x3(%rbp,%rax,4),%ecx
   0x00000000004013d4 <+304>:   inc    %rax
   0x00000000004013d7 <+307>:   lea    -0x20(%rcx),%esi
   0x00000000004013da <+310>:   cmp    $0x5e,%sil
   0x00000000004013de <+314>:   jbe    0x4013cb <main+295>
   0x00000000004013e0 <+316>:   mov    0x2cd9(%rip),%rdi        # 0x4040c0 <stderr@@GLIBC_2.2.5>
   0x00000000004013e7 <+323>:   lea    0xe1e(%rip),%rdx        # 0x40220c
   0x00000000004013ee <+330>:   mov    $0x1,%esi
   0x00000000004013f3 <+335>:   xor    %eax,%eax
   0x00000000004013f5 <+337>:   call   0x401250 <__fprintf_chk@plt>
   0x00000000004013fa <+342>:   jmp    0x40135b <main+183>
   0x00000000004013ff <+347>:   mov    %rbp,%rsi
   0x0000000000401402 <+350>:   mov    %r12,%rdi
   0x0000000000401405 <+353>:   lea    0x2c14(%rip),%r12        # 0x404020 <desired_output>
   0x000000000040140c <+360>:   xor    %ebx,%ebx
   0x000000000040140e <+362>:   call   0x4016cb <display>
   0x0000000000401413 <+367>:   mov    0x8(%rsp),%r13d
   0x0000000000401418 <+372>:   mov    0x10(%rsp),%r14
   0x000000000040141d <+377>:   cmp    $0x4,%r13d
   0x0000000000401421 <+381>:   sete   %bl
   0x0000000000401424 <+384>:   xor    %ebp,%ebp
   0x0000000000401426 <+386>:   xor    %r15d,%r15d
   0x0000000000401429 <+389>:   cmp    $0x4,%ebp
   0x000000000040142c <+392>:   je     0x40146c <main+456>
   0x000000000040142e <+394>:   cmp    %ebp,%r13d
   0x0000000000401431 <+397>:   jbe    0x40146c <main+456>
   0x0000000000401433 <+399>:   imul   $0x18,%rbp,%rdi
   0x0000000000401437 <+403>:   mov    0x13(%r14,%rdi,1),%al
   0x000000000040143c <+408>:   cmp    0x13(%r12),%al
   0x0000000000401441 <+413>:   cmovne %r15d,%ebx
   0x0000000000401445 <+417>:   cmp    $0x20,%al
   0x0000000000401447 <+419>:   je     0x401463 <main+447>
   0x0000000000401449 <+421>:   cmp    $0xa,%al
   0x000000000040144b <+423>:   je     0x401463 <main+447>
   0x000000000040144d <+425>:   add    %r14,%rdi
   0x0000000000401450 <+428>:   mov    $0x18,%edx
   0x0000000000401455 <+433>:   mov    %r12,%rsi
   0x0000000000401458 <+436>:   call   0x4011e0 <memcmp@plt>
   0x000000000040145d <+441>:   test   %eax,%eax
   0x000000000040145f <+443>:   cmovne %r15d,%ebx
   0x0000000000401463 <+447>:   inc    %rbp
   0x0000000000401466 <+450>:   add    $0x18,%r12
   0x000000000040146a <+454>:   jmp    0x401429 <main+389>
   0x000000000040146c <+456>:   test   %ebx,%ebx
   0x000000000040146e <+458>:   je     0x401477 <main+467>
   0x0000000000401470 <+460>:   xor    %eax,%eax
   0x0000000000401472 <+462>:   call   0x401586 <win>
   0x0000000000401477 <+467>:   mov    0x18(%rsp),%rax
   0x000000000040147c <+472>:   xor    %fs:0x28,%rax
   0x0000000000401485 <+481>:   je     0x40148c <main+488>
   0x0000000000401487 <+483>:   call   0x401190 <__stack_chk_fail@plt>
   0x000000000040148c <+488>:   add    $0x28,%rsp
   0x0000000000401490 <+492>:   xor    %eax,%eax
   0x0000000000401492 <+494>:   pop    %rbx
   0x0000000000401493 <+495>:   pop    %rbp
   0x0000000000401494 <+496>:   pop    %r12
   0x0000000000401496 <+498>:   pop    %r13
   0x0000000000401498 <+500>:   pop    %r14
   0x000000000040149a <+502>:   pop    %r15
   0x000000000040149c <+504>:   ret
End of assembler dump.
```
-------------------------------------------

#### At <+142> it loads 8 bytes to read, so thats the header length. At <+162> it compares a long(4 bytes) of the magic number. Which means it has to be packed into '<I'. At <+191> the version is 2 and compares a word(2 bytes), so packed into '<H'.
```asm
   0x0000000000401332 <+142>:   mov    $0x8,%edx
   0x0000000000401337 <+147>:   lea    0xe26(%rip),%rcx        # 0x402164
   0x000000000040133e <+154>:   mov    %r12,%rsi
   0x0000000000401341 <+157>:   call   0x40167b <read_exact>
   0x0000000000401346 <+162>:   cmpl   $0x474d4963,(%rsp)

   0x0000000000401363 <+191>:   cmpw   $0x2,0x4(%rsp)
```
--------------------------------------------

#### The width and height are checked at 1 byte and multiplied by 4 so its (w * h) * 4, which confirms the rgbC structure. These will be '<B'. This will be relevent below.
```asm
   0x000000000040137a <+214>:   movzbl 0x6(%rsp),%ebx
   0x000000000040137f <+219>:   movzbl 0x7(%rsp),%edx
   0x0000000000401384 <+224>:   imul   %edx,%ebx
   0x0000000000401387 <+227>:   movslq %ebx,%rbx
   0x000000000040138a <+230>:   shl    $0x2,%rbx
```
---------------------------------------------

#### At <+353> is the desired output. At <+389> there's a loop with 4 iterations. It moves 24 bytes(0x18) at <+428>, meaning it checks a total of 96 bytes. 
```asm
   0x0000000000401405 <+353>:   lea    0x2c14(%rip),%r12        # 0x404020 <desired_output>

   0x0000000000401429 <+389>:   cmp    $0x4,%ebp
   0x000000000040142c <+392>:   je     0x40146c <main+456>
   0x000000000040142e <+394>:   cmp    %ebp,%r13d
   0x0000000000401431 <+397>:   jbe    0x40146c <main+456>
   0x0000000000401433 <+399>:   imul   $0x18,%rbp,%rdi
   0x0000000000401437 <+403>:   mov    0x13(%r14,%rdi,1),%al
   0x000000000040143c <+408>:   cmp    0x13(%r12),%al
   0x0000000000401441 <+413>:   cmovne %r15d,%ebx
   0x0000000000401445 <+417>:   cmp    $0x20,%al
   0x0000000000401447 <+419>:   je     0x401463 <main+447>
   0x0000000000401449 <+421>:   cmp    $0xa,%al
   0x000000000040144b <+423>:   je     0x401463 <main+447>
   0x000000000040144d <+425>:   add    %r14,%rdi
   0x0000000000401450 <+428>:   mov    $0x18,%edx
   0x0000000000401455 <+433>:   mov    %r12,%rsi
   0x0000000000401458 <+436>:   call   0x4011e0 <memcmp@plt>
   0x000000000040145d <+441>:   test   %eax,%eax
   0x000000000040145f <+443>:   cmovne %r15d,%ebx
   0x0000000000401463 <+447>:   inc    %rbp
   0x0000000000401466 <+450>:   add    $0x18,%r12
   0x000000000040146a <+454>:   jmp    0x401429 <main+389>
```
---------------------------------------------

#### Checking the <desired_output> for what it wants. This shows the ANSI color sequence via ```$$\texttt{\x1B[38;2;R;G;B m C \x1B[0m}$$```
```asm
(gdb) x/96xb 0x404020
0x404020 <desired_output>:      0x1b    0x5b    0x33    0x38    0x3b    0x32    0x3b    0x30
0x404028 <desired_output+8>:    0x36    0x31    0x3b    0x31    0x35    0x30    0x3b    0x31
0x404030 <desired_output+16>:   0x32    0x33    0x6d    0x63    0x1b    0x5b    0x30    0x6d
0x404038 <desired_output+24>:   0x1b    0x5b    0x33    0x38    0x3b    0x32    0x3b    0x31
0x404040 <desired_output+32>:   0x31    0x30    0x3b    0x31    0x36    0x36    0x3b    0x32
0x404048 <desired_output+40>:   0x31    0x31    0x6d    0x49    0x1b    0x5b    0x30    0x6d
0x404050 <desired_output+48>:   0x1b    0x5b    0x33    0x38    0x3b    0x32    0x3b    0x30
0x404058 <desired_output+56>:   0x37    0x31    0x3b    0x32    0x33    0x36    0x3b    0x31
0x404060 <desired_output+64>:   0x39    0x31    0x6d    0x4d    0x1b    0x5b    0x30    0x6d
0x404068 <desired_output+72>:   0x1b    0x5b    0x33    0x38    0x3b    0x32    0x3b    0x32
0x404070 <desired_output+80>:   0x32    0x34    0x3b    0x31    0x36    0x39    0x3b    0x31
0x404078 <desired_output+88>:   0x31    0x36    0x6d    0x47    0x1b    0x5b    0x30    0x6d
```
-------------------------------------------

#### Breaking this down, start with the first block of 24 bytes. 1b/5b are the start of the sequence $\texttt{\x1B[}$. 33/38/3b/32/3b is the fixed true color mode prefix 38;2;. 30/36/31 (061) for Red. 31/35/30 (150) for Green. 31/32/33 (123) for Blue. 6d (m) is the end of the color command. 63 (C) is the ascii character. 1b/5b/30/6d is the reset. Repeat for the next 3 blocks.
```asm
0x404020 <desired_output>:      0x1b    0x5b    0x33    0x38    0x3b    0x32    0x3b    0x30
0x404028 <desired_output+8>:    0x36    0x31    0x3b    0x31    0x35    0x30    0x3b    0x31
0x404030 <desired_output+16>:   0x32    0x33    0x6d    0x63    0x1b    0x5b    0x30    0x6d
```
--------------------------------------------

#### From here just craft the script. Initially I thought it needed 80 bytes of padding, but then went back to the section where its added mentioned above. The program sets ebx to width and edx to height and runs imul(which is w * h) so its 1 byte (remember they were 1 byte each). It then does shl x02, thats 2^2(4), meaning ((w * h) * 4) so its 1 * 4 which is 4. The only way to have a 4 byte length is with a 1 high image:
```python
import struct

w = 4
h = 1
total = w * h

magic = struct.pack('<I', 0x474d4963)              
version = struct.pack('<H', 2)
width = struct.pack('<B', w)
height = struct.pack('<B', h) 

pixels = b'\x3D\x96\x7B\x63' + b'\x6E\xA6\xD3\x49' + b'\x47\xEC\xBF\x4D' + b'\xE0\xA9\x74\x47'

cimg_base = magic + version + width + height

cimg = cimg_base + pixels

filename = "/home/hacker/s.cimg"
with open(filename, "wb") as f:
    f.write(cimg)
```
