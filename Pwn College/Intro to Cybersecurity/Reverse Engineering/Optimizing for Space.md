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

#### Next I looked at the handle functions. The first reads the final width and height and loads an entire image. The second reads patches and applies them based om x, y coordinates. c, b, d and e are x, y, width and height.
```asm
   0x0000000000401998 <+300>:   movzbl 0xc(%rsp),%eax
   0x000000000040199d <+305>:   cmp    %r13d,%eax
   0x00000000004019a0 <+308>:   jle    0x401a4d <handle_52965+481>
   0x00000000004019a6 <+314>:   xor    %r14d,%r14d
   0x00000000004019a9 <+317>:   movzbl 0xb(%rsp),%ecx
   0x00000000004019ae <+322>:   cmp    %r14d,%ecx
   0x00000000004019b1 <+325>:   jle    0x401a45 <handle_52965+473>
   0x00000000004019b7 <+331>:   movzbl 0xd(%rsp),%eax
   0x00000000004019bc <+336>:   movzbl 0xe(%rsp),%ebx
   0x00000000004019c1 <+341>:   imul   %r13d,%ecx
   0x00000000004019c5 <+345>:   mov    %r15,%rdi
   0x00000000004019c8 <+348>:   movzbl 0x6(%r12),%esi
   0x00000000004019ce <+354>:   lea    0x781(%rip),%r8        # 0x402156
```
-----------------------------------------

#### The rest of the functions are unimportant. The idea now is to craft a script that will pull all of the desired_input pixel data, iterate through and grab the colors and characters. Then map out the grid getting x and y coords for each. From there it must find the most efficient patches to make. The logic is every patch is a minimum of 6 bytes, 2 for the directive and 4 for the patch info (x,y,w,h). It checks the whole grid through x and y and finds where the least amount of new patches is needed by grouping pixels that are close together and using fill pixels if the gaps are short. It then compares all the patches it created and picks the best. It then finds the proper width and height for the image.
```python
from pwn import *
import os
import struct
import re
import math

context.log_level = 'error'

def get_pixels():
    io = process(['gdb', '/challenge/cimg'])

    io.sendline(b'b main')
    io.recvuntil(b'(gdb)')
    io.sendline(b'r')
    io.recvuntil(b'(gdb)')
    io.sendline(b'disas')
    io.recvuntil(b'(gdb)')
    io.sendline(b'c')
    io.recvuntil(b'(gdb)')

    size = 43774
    start = 0x404020
    end = hex(int(start) + size)
    file = '/tmp/dump.bin'

    disas_cimg = (f'dump binary memory {file} {start} {end}')
    io.sendline(disas_cimg.encode())
    io.recvuntil(b'(gdb)')
    io.sendline(b'q')
    io.sendline(b'y')
    io.close()

    with open(file, 'rb') as f:
            desired_cimg = f.read()

    pixel_size = 24
    pixel_map = {}
    pixel_rgba_search = re.compile(rb'\x1b\[38;2;(?P<r>\d{1,3});(?P<g>\d{1,3});(?P<b>\d{1,3})m(?P<a>.)(?:\\x1b\[0m)?', re.DOTALL)

    n_row = False
    x, y = 0, 0

    for i in range(0, size, pixel_size):
        pixel_block = desired_cimg[i : i + pixel_size]
        pixel_rgba = pixel_rgba_search.match(pixel_block)
        r = int(pixel_rgba.group('r').decode())
        g = int(pixel_rgba.group('g').decode())
        b = int(pixel_rgba.group('b').decode())
        a = pixel_rgba.group('a')

        if a != b' ':
            pixel_map[(x, y)] = {'r': r, 'g': g, 'b': b, 'a': a}

            if a == b'.':

                if n_row == False:
                    n_row = True

                else:
                    x = -1
                    y += 1
                    n_row = False

            elif a == b'|' and (r == 255 and g == 255 and b == 255):

                if n_row == False:
                    n_row = True

                else:
                    x = -1
                    y += 1
                    n_row = False
        x += 1

    return pixel_map

def find_best_run(remaining_pixels, full_map):
    best_run = None
    max_pixels = 0

    candidates = sorted(list(remaining_pixels))
    
    for (start_x, start_y) in candidates:
        
        curr_x = start_x
        h_pixels = []

        while True:

            if (curr_x, start_y) in full_map:

                if (curr_x, start_y) in remaining_pixels:
                    h_pixels.append((curr_x, start_y))

                curr_x += 1

            elif (curr_x + 1, start_y) in full_map:
                curr_x += 1

            else:
                break

            if curr_x - start_x >= 76: 
                break 
            
        curr_y = start_y
        v_pixels = []

        while True:

            if (start_x, curr_y) in full_map:

                if (start_x, curr_y) in remaining_pixels:
                    v_pixels.append((start_x, curr_y))

                curr_y += 1

            elif (start_x, curr_y + 1) in full_map:
                curr_y += 1

            else:
                break

            if curr_y - start_y >= 24: 
                break
        
        if len(h_pixels) > max_pixels:
            max_pixels = len(h_pixels)
            best_run = {'x': start_x, 'y': start_y, 'w': (curr_x - start_x), 'h': 1, 'type': 'H', 'pixels': h_pixels}
            
        if len(v_pixels) > max_pixels:
            max_pixels = len(v_pixels)
            best_run = {'x': start_x, 'y': start_y, 'w': 1, 'h': (curr_y - start_y), 'type': 'V', 'pixels': v_pixels}
            
    return best_run

def solve_optimized(pixel_map):
    targets = set(pixel_map.keys())
    commands = []

    while targets:
        best_cmd = find_best_run(targets, pixel_map)
        
        if not best_cmd:
            break
            
        data_bytes = b""
        bx, by = best_cmd['x'], best_cmd['y']
        
        if best_cmd['type'] == 'H':

            for i in range(best_cmd['w']):
                p = pixel_map.get((bx + i, by))
                
                if not p: 
                    p = {'r':255, 'g':255, 'b':255, 'a':b' '} 

                data_bytes += struct.pack("BBB", p['r'], p['g'], p['b']) + p['a']

        else:
            for i in range(best_cmd['h']):
                p = pixel_map.get((bx, by + i))

                if not p: 
                    p = {'r':255, 'g':255, 'b':255, 'a':b' '}

                data_bytes += struct.pack("BBB", p['r'], p['g'], p['b']) + p['a']

        commands.append({'x': bx, 'y': by, 'w': best_cmd['w'], 'h': best_cmd['h'], 'data': data_bytes})
        
        for p in best_cmd['pixels']:
            targets.discard(p)
            
    return commands

def generate_file():
    pixel_map = get_pixels()
    commands = solve_optimized(pixel_map)

    size = 43774
    total_pixels = (size // 24) + 1
    max_dimension = 255
    directive = 52965
    file_check = False

    for width in range(1, max_dimension + 1):
        if total_pixels % width == 0:
            height = total_pixels // width
            
            if height <= max_dimension:
                package = struct.pack('<IHBBI', 0x474d4963, 3, width, height, len(commands))
                
                for cmd in commands:
                    package += struct.pack("<H", directive)
                    package += struct.pack("B", cmd['x'])
                    package += struct.pack("B", cmd['y'])
                    package += struct.pack("B", cmd['w'])
                    package += struct.pack("B", cmd['h'])
                    package += cmd['data']

                file_size = len(package)
    
                if file_size <= 1337:
                    if file_check == False:
                        print(f'File is {file_size} and within the 1337 byte limit.')
                        file_check = True

                    with open("s.cimg", "wb") as f:
                        f.write(package)

                    io = process(['/challenge/cimg', 's.cimg'])
                    out = io.recvall(timeout=2)
                    flag_found = re.search(rb'pwn\.college\{.*?\}', out)

                    if flag_found:
                        flag = flag_found.group(0).decode()
                        print(f"Found flag with width {width} and height {height}.\n")
                        print(flag)
                        exit()
                
                else:
                    print(f'File exceeds limit by {file_size - 1337} bytes.')
                    exit()
    
if __name__ == "__main__":
    generate_file()
```
