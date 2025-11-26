# File Formats: Magic Numbers (C)

#### First set a break at main. Run a dummy img and disass main:
```asm
(gdb) b main
Breakpoint 1 at 0x401508
(gdb) run dummy.cimg
Starting program: /challenge/cimg dummy.cimg

Breakpoint 1, 0x0000000000401508 in main ()
(gdb) disass main
Dump of assembler code for function main:
   0x0000000000401500 <+0>:     endbr64
   0x0000000000401504 <+4>:     push   %rbp
   0x0000000000401505 <+5>:     mov    %rsp,%rbp
=> 0x0000000000401508 <+8>:     push   %rbx
   0x0000000000401509 <+9>:     sub    $0x38,%rsp
   0x000000000040150d <+13>:    mov    %edi,-0x24(%rbp)
   0x0000000000401510 <+16>:    mov    %rsi,-0x30(%rbp)
   0x0000000000401514 <+20>:    mov    %rdx,-0x38(%rbp)
   0x0000000000401518 <+24>:    mov    %fs:0x28,%rax
   0x0000000000401521 <+33>:    mov    %rax,-0x18(%rbp)
   0x0000000000401525 <+37>:    xor    %eax,%eax
   0x0000000000401527 <+39>:    movl   $0x0,-0x1c(%rbp)
--Type <RET> for more, q to quit, c to continue without paging--c
   0x000000000040152e <+46>:    movl   $0x1,-0x20(%rbp)
   0x0000000000401535 <+53>:    cmpl   $0x1,-0x24(%rbp)
   0x0000000000401539 <+57>:    jle    0x4015b7 <main+183>
   0x000000000040153b <+59>:    mov    -0x30(%rbp),%rax
   0x000000000040153f <+63>:    add    $0x8,%rax
   0x0000000000401543 <+67>:    mov    (%rax),%rbx
   0x0000000000401546 <+70>:    mov    -0x30(%rbp),%rax
   0x000000000040154a <+74>:    add    $0x8,%rax
   0x000000000040154e <+78>:    mov    (%rax),%rax
   0x0000000000401551 <+81>:    mov    %rax,%rdi
   0x0000000000401554 <+84>:    call   0x401160 <strlen@plt>
   0x0000000000401559 <+89>:    sub    $0x5,%rax
   0x000000000040155d <+93>:    add    %rbx,%rax
   0x0000000000401560 <+96>:    lea    0xb85(%rip),%rsi        # 0x4020ec
   0x0000000000401567 <+103>:   mov    %rax,%rdi
   0x000000000040156a <+106>:   call   0x4011d0 <strcmp@plt>
   0x000000000040156f <+111>:   test   %eax,%eax
   0x0000000000401571 <+113>:   je     0x40158e <main+142>
   0x0000000000401573 <+115>:   lea    0xb7e(%rip),%rdi        # 0x4020f8
   0x000000000040157a <+122>:   mov    $0x0,%eax
   0x000000000040157f <+127>:   call   0x401190 <printf@plt>
   0x0000000000401584 <+132>:   mov    $0xffffffff,%edi
   0x0000000000401589 <+137>:   call   0x401210 <exit@plt>
   0x000000000040158e <+142>:   mov    -0x30(%rbp),%rax
   0x0000000000401592 <+146>:   add    $0x8,%rax
   0x0000000000401596 <+150>:   mov    (%rax),%rax
   0x0000000000401599 <+153>:   mov    $0x0,%esi
   0x000000000040159e <+158>:   mov    %rax,%rdi
   0x00000000004015a1 <+161>:   mov    $0x0,%eax
   0x00000000004015a6 <+166>:   call   0x401200 <open@plt>
   0x00000000004015ab <+171>:   mov    $0x0,%esi
   0x00000000004015b0 <+176>:   mov    %eax,%edi
   0x00000000004015b2 <+178>:   call   0x401180 <dup2@plt>
   0x00000000004015b7 <+183>:   lea    -0x1c(%rbp),%rax
   0x00000000004015bb <+187>:   mov    $0xffffffff,%r8d
   0x00000000004015c1 <+193>:   lea    0xb4f(%rip),%rcx        # 0x402117
   0x00000000004015c8 <+200>:   mov    $0x4,%edx
   0x00000000004015cd <+205>:   mov    %rax,%rsi
   0x00000000004015d0 <+208>:   mov    $0x0,%edi
   0x00000000004015d5 <+213>:   call   0x40143d <read_exact>
   0x00000000004015da <+218>:   movzbl -0x1c(%rbp),%eax
   0x00000000004015de <+222>:   cmp    $0x28,%al
   0x00000000004015e0 <+224>:   jne    0x4015fa <main+250>
   0x00000000004015e2 <+226>:   movzbl -0x1b(%rbp),%eax
   0x00000000004015e6 <+230>:   cmp    $0x7e,%al
   0x00000000004015e8 <+232>:   jne    0x4015fa <main+250>
   0x00000000004015ea <+234>:   movzbl -0x1a(%rbp),%eax
   0x00000000004015ee <+238>:   cmp    $0x6d,%al
   0x00000000004015f0 <+240>:   jne    0x4015fa <main+250>
   0x00000000004015f2 <+242>:   movzbl -0x19(%rbp),%eax
   0x00000000004015f6 <+246>:   cmp    $0x36,%al
   0x00000000004015f8 <+248>:   je     0x401610 <main+272>
   0x00000000004015fa <+250>:   lea    0xb34(%rip),%rdi        # 0x402135
   0x0000000000401601 <+257>:   call   0x401140 <puts@plt>
   0x0000000000401606 <+262>:   mov    $0xffffffff,%edi
   0x000000000040160b <+267>:   call   0x401210 <exit@plt>
   0x0000000000401610 <+272>:   cmpl   $0x0,-0x20(%rbp)
   0x0000000000401614 <+276>:   je     0x401620 <main+288>
   0x0000000000401616 <+278>:   mov    $0x0,%eax
   0x000000000040161b <+283>:   call   0x401316 <win>
   0x0000000000401620 <+288>:   mov    $0x0,%eax
   0x0000000000401625 <+293>:   mov    -0x18(%rbp),%rcx
   0x0000000000401629 <+297>:   xor    %fs:0x28,%rcx
   0x0000000000401632 <+306>:   je     0x401639 <main+313>
   0x0000000000401634 <+308>:   call   0x401170 <__stack_chk_fail@plt>
   0x0000000000401639 <+313>:   add    $0x38,%rsp
   0x000000000040163d <+317>:   pop    %rbx
   0x000000000040163e <+318>:   pop    %rbp
   0x000000000040163f <+319>:   ret
End of assembler dump.
```
---------------------------------------------

#### Looking for cmp calls, first one is <+53>:    cmpl   $0x1,-0x24(%rbp)
#### Checking this comment shows its .cimg
```asm
(gdb) x/s 0x4020ec
0x4020ec:       ".cimg"
```
--------------------------------------------

#### Next theres 4 cmp calls, the hex code show what the magic number is:
```asm
0x00000000004015da <+218>:   movzbl -0x1c(%rbp),%eax
   0x00000000004015de <+222>:   cmp    $0x28,%al
   0x00000000004015e0 <+224>:   jne    0x4015fa <main+250>
   0x00000000004015e2 <+226>:   movzbl -0x1b(%rbp),%eax
   0x00000000004015e6 <+230>:   cmp    $0x7e,%al
   0x00000000004015e8 <+232>:   jne    0x4015fa <main+250>
   0x00000000004015ea <+234>:   movzbl -0x1a(%rbp),%eax
   0x00000000004015ee <+238>:   cmp    $0x6d,%al
   0x00000000004015f0 <+240>:   jne    0x4015fa <main+250>
   0x00000000004015f2 <+242>:   movzbl -0x19(%rbp),%eax
   0x00000000004015f6 <+246>:   cmp    $0x36,%al
```

---------------------------------------------
#### Craft the file and submit as an argument:
```sh
echo -n "(~m6" > exploit.cimg
/challenge/cimg exploit.cimg
```
