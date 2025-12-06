# Accessing Resources

#### This one was easy but I got caught in being comfortable with how the challenges progressed from before. I read through the new handle seeing that it allows a file to be opened instead of having to list the bytes directly. So I adapted the prior script to create files for each color and have them stamped. It didn't work, checking more I saw that maybe it wanted the background to match (0, 0, 0) so I changed the script to include all the empty spaces and stamp that as well. Also didn't work. Then after trying to figure out how I could put the whole image in one file, which is impossible by the way, I realized that handle_5 can open any file, not just ones I create.
```python
from pwn import *
import struct
import re

def gen_flag(read_len):
    cimg = b"cIMG" + struct.pack("<HBBI", 4, 100, 10, 2)
    s_id = 0
    file = b"/flag\x00"
    
    d_count = struct.pack("<HBBB", 5, s_id, read_len, 1)
    
    pad = b'\x00' * (255 - len(file))
    d_count += file + pad  
    cimg += d_count
    
    stamp = struct.pack("<HBBBBBBBBB", 4, s_id, 255, 255, 255, 0, 0, 1, 1, 0)
    cimg += stamp

    return cimg

def gen_file():
    
    chars = 60
    
    data = gen_flag(chars)
    with open("s.cimg", "wb") as f:
        f.write(data)

    io = process(['/challenge/cimg', 's.cimg'])
    resp = io.recvall(timeout=1)
    flag = re.search(rb'')

if __name__ == '__main__':
    gen_file()
```
