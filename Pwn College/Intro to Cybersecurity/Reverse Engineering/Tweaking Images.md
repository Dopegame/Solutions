# Tweaking Images

#### Rather than deal with hex edits and such, just reverse engineer the generate image file:
```python
import struct
import sys

with open("/challenge/flag.cimg", "rb") as f:
    data = f.read()

head = 12
directives = struct.unpack("<I", data[8:12])[0]  

size = 10
grid = {}
max_x = 0
max_y = 0

cur_offset = head

for _ in range(directives):
    chunk = data[cur_offset : cur_offset + size]
    cur_offset += size

    parts = struct.unpack("<HBBBBBBBB", chunk)
        
    x = parts[1]
    y = parts[2]
    chars = parts[8]
        
    grid[(x, y)] = chr(chars)
        
    if x > max_x: 
        max_x = x

    if y > max_y: 
        max_y = y

for y in range(max_y + 1):
    line = ""
        
    for x in range(max_x + 1):
        line += grid.get((x, y), " ")
    print(line)
