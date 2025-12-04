# Optimizing for Space

#### This and the last challenge are the same, this one just requires a highly efficient script. To start launch gdb and disassemble main. Header is still 12 bytes long. Magic number is still 'cIMG'. Version is still 3.
```asm
0x0000000000401335 <+145>:   mov    $0xc,%edx
0x0000000000401349 <+165>:   cmpl   $0x474d4963,0x10(%rsp)
0x0000000000401367 <+195>:   cmpw   $0x3,0x14(%rsp)
```
----------------------------------------

#### At <+339> the start of the loop check for pixels begins. The cmp is for 1824 pixels, meaning the total of desired_output is 43774. Desired_output can now be memory dumped for the image. The program sets the flag to 0 if the check fails the exact pixel count seen at <+370>.
```asm
0x00000000004013f7 <+339>:   lea    0x2c22(%rip),%r12        # 0x404020 <desired_output>
0x00000000004013fe <+346>:   xor    %ebx,%ebx
0x0000000000401400 <+348>:   call   0x401a71 <display>
0x0000000000401405 <+353>:   mov    0x1c(%rsp),%r14d
0x000000000040140a <+358>:   mov    0x20(%rsp),%r13
0x000000000040140f <+363>:   cmp    $0x720,%r14d
0x0000000000401416 <+370>:   sete   %bl
```
-----------------------------------------

#### The max cimg file size is 1337 found at <+446>.
```asm
0x0000000000401462 <+446>:   cmpq   $0x539,0xd703(%rip)        # 0x40eb70 <total_data>
```
----------------------------------------

#### Now comes the actual reverse engineering part. To find out how the program behaves, each function needs disassembling. I started with initialzation_framebuffer and discovered that it actually fills the entire 'canvas' with a preloaded pixel of 255;255;255' '. This means the image already has the required pixels for any actual space thats a pixel, and only the visible characters need to be printed.
```asm
0x0000000000401b37 <+101>:   cmp    %ebx,0xc(%r12)
0x0000000000401b3c <+106>:   jbe    0x401b92 <initialize_framebuffer+192>
0x0000000000401b3e <+108>:   push   %rax
0x0000000000401b3f <+109>:   mov    $0x1,%edx
0x0000000000401b44 <+114>:   mov    $0xff,%r9d
0x0000000000401b4a <+120>:   mov    %rbp,%rdi
0x0000000000401b4d <+123>:   push   $0x20
0x0000000000401b4f <+125>:   lea    0x600(%rip),%r8        # 0x402156
0x0000000000401b56 <+132>:   mov    $0x19,%ecx
0x0000000000401b5b <+137>:   xor    %eax,%eax
0x0000000000401b5d <+139>:   push   $0xff
0x0000000000401b62 <+144>:   mov    $0x19,%esi
0x0000000000401b67 <+149>:   push   $0xff
0x0000000000401b6c <+154>:   call   0x401150 <__snprintf_chk@plt>
```
----------------------------------------

#### Next I looked at the handle functions. One of the functions just checks pixels and prints them normal
