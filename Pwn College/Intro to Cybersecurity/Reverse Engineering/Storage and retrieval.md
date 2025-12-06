# Storage and Retrieval

#### Same as the other challenges, except now instead of patching it uses sprites. The file size allowed is also much smaller. After doing the same disassembly and searching through functions theres 4 handles now. Two are unimportant but 3 and 4 are. Handle_3 is the write function and Handle_4 is the "stamp" function. The script follows most of the same logic as the last but this time I have it group pixels by color and make "stamps". Originally the file was 9 bytes too big, but then I realized the borders could be stamped mulitple times rather than call separate write functions for them. The sides are identical, meaning 1 write and 2 stamps. Same with the dashes, split them in half and thats 4 stamps. This is much cheaper. Also started using functions more rather than inline code the entire thing.
```python
from pwn import *
import subprocess
import sys
import re
import struct

exe = '/challenge/cimg'
context.binary = exe
elf = ELF(exe)

def disas_gdb(path, name):
    desired_output = 0
    output = subprocess.check_output(['readelf', '-sW', path], text=True)

    for line in output.splitlines():

        if name in line:
            parts = line.split()

            if len(parts) >= 8 and parts[7] == name:
                desired_output = int(parts[2])
    
    if desired_output == 0:
        print("[-] Failed to determine size.")
        return 
    
    addr = elf.symbols[name]
    cimg_pixels = elf.read(addr, desired_output)
   
    return cimg_pixels, desired_output

def pixel_list():
    desired_cimg, desired_size = disas_gdb(exe, 'desired_output')
    pixel_size = 24
    num_pixels = desired_size // pixel_size

    pixels = {}
    beg = False
    width = -1

    for i in range(num_pixels):
        pixel = desired_cimg[i*pixel_size : (i+1)*pixel_size]
        
        r = int(pixel[7:10])
        g = int(pixel[11:14])
        b = int(pixel[15:18])
        c = pixel[19] 
        a = pixel[19:20]

        if c != 32 and c != 0:
            
            if c == 46:

                if beg == False:
                    beg = True
                    x, y = 0, 0
    
                else:
                    width = x + 1

            pixels[(x, y)] = {'r': r, 'g': g, 'b': b, 'c': c,'a': a}
            
        if x == width - 1:
            y += 1
            x = -1

        x += 1
    
    height = y

    return pixels, width, height

def sprite_package(id, width, height, data):
    header = struct.pack("<HBBB", 3, id, width, height)
    return header + data

def stamp_package(id, x, y , color=(255, 255, 255)):
    r, g, b = color
    return struct.pack("<HBBBBBB", 4, id, r, g, b, x, y)

def gen_sprite():
    pixels, width, height = pixel_list()   
    packages = b''
    d_count = 0
    col_b = b''
    dash_b = b''
    half_w = width // 2
    half_h = height // 2

    for x in range(1, width - 2):
        dash = pixels[(x, 0)]['c']
        dash_b += bytes([dash])

    color = (255, 255, 255)
    s_id = 0
    half_d = dash_b[:half_w]

    packages += sprite_package(s_id, half_w, 1, half_d)
    packages += stamp_package(s_id, 1, 0, color)
    packages += stamp_package(s_id, 1 + half_w, 0, color)
    packages += stamp_package(s_id, 1, height - 1, color)
    packages += stamp_package(s_id, 1 + half_w, height - 1, color)
    d_count += 5

    s_id += 1

    for y in range(height):
        col = pixels[(0, y)]['c']
        col_b += bytes([col])
        
    packages += sprite_package(s_id, 1, height, col_b)
    packages += stamp_package(s_id, 0, 0, color)
    packages += stamp_package(s_id, width - 1, 0, color)
    d_count += 3
    s_id += 1

    checked = set()
    stamp_colors = {}

    for y in range(1, height - 2):
        for x in range(1, width - 2):
            if (x, y) in pixels and (x, y) not in checked:
                pix = pixels[(x, y)]
                rgb_key = (pix['r'], pix['g'], pix['b'])

                if rgb_key not in stamp_colors:
                    stamp_colors[rgb_key] = []
                
                stamp_colors[rgb_key].append((x, y))
                
            checked.add((x, y))

    stamp_bounds = {}

    for rgb_key, pix_coords in stamp_colors.items():
        
        min_x = min(p[0] for p in pix_coords)
        min_y = min(p[1] for p in pix_coords)
        max_x = max(p[0] for p in pix_coords)
        max_y = max(p[1] for p in pix_coords)

        w = max_x - min_x + 1
        h = max_y - min_y + 1

        stamp_bounds[rgb_key] = {'x': min_x, 'y': min_y, 'w': w, 'h': h}
        
    stamps = {}

    for rgb_key, pix_coords in stamp_colors.items():
        bounds = stamp_bounds[rgb_key]
        pixel_set = set(pix_coords)
        curr_stamp = b''

        for y in range(bounds['y'], bounds['y'] + bounds['h']):
            for x in range(bounds['x'], bounds['x'] + bounds['w']):

                if (x, y) in pixel_set:
                    cs = pixels[(x, y)]['c']
                    curr_stamp += bytes([cs])

                else:
                    curr_stamp += b' '

        packages += sprite_package(s_id, bounds['w'], bounds['h'], curr_stamp)
        packages += stamp_package(s_id, bounds['x'], bounds['y'], rgb_key)
        d_count += 2
        s_id += 1

    return packages, d_count, width, height

def gen_file():
    packages, d_count, width, height = gen_sprite()
    cimg = b"cIMG" + struct.pack("<HBBI", 3, width, height, d_count) + packages
    
    if len(cimg) > 400:
        print(f'File is {len(cimg) - 400} bytes too big.')
    else:
        print(f'File is within range: {len(cimg)}')

    with open("s.cimg", "wb") as f:
        f.write(cimg)

if __name__ == '__main__':
    gen_file()
```
