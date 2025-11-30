# Internal State (C)

#### This time theres an actual image to construct and consists of 1020 pixels. Same as previous challenges, gdb into the program and disassemble. This time dump binary memory of desired output into a tmp file as its 24480 bytes. From there, parse through and grab the actual rgba values. I created a script to iterate and find all possible combos of width and height and check if which works. Turns out after reading through the source, the width and height dont matter as long as they multiply to equal the total pixels required (1020). From there just add values and then struct into the cimg file:
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

start = b'0x404020'
end = b'0x409fc0'
file = '/tmp/dump.bin'
size = 24480

disas_cimg = (f'dump binary memory {file} {start.decode()} {end.decode()}')
io.sendline(disas_cimg.encode())
io.recvuntil(b'(gdb)')
io.sendline(b'q')
io.sendline(b'y')

with open(file, 'rb') as f:
        desired_cimg = f.read()
if len(desired_cimg) == size:
    print("Size is correct.")

pixel_data = 24480
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

total_pixels = 1020
max_dimension = 255

for width in range(1, max_dimension + 1):
    if total_pixels % width == 0:
        height = total_pixels // width
        
        if height <= max_dimension:
            w = width
            h = height
            magic = struct.pack('<I', 0x474d4963)
            version = struct.pack('<H', 2)
            width = struct.pack('<B', w)
            height = struct.pack('<B', h)

            with open("s.cimg", "wb") as f:
                    f.write(magic + version + width + height + pixels)

            io.close()
            io = process(['/challenge/cimg', 's.cimg'])
            out = io.recvall(timeout=2)
            flag = re.search(rb'pwn\.college\{.*?\}', out).group().decode().strip()
            if flag:
                print(f"Found flag with width {w} and height {h}.")
                break
        
print(flag)
