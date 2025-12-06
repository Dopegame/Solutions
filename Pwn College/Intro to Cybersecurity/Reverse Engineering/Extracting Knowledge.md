# Extracting Knowledge

#### Not sure what exactly the challenge intended but this was the path I took. Recreate the image creation file and then reverse it.
```python
import struct
import subprocess
import os
import string

charset = string.printable
lookup = {}

for char in charset:
    output = subprocess.check_output(["/usr/bin/figlet", "-fascii9"], input=char.encode()).split(b"\n")[:-1]
    data = b"".join(output)
    lookup[data] = char

def parse_cimg(filename):
    if not os.path.exists(filename):
        print(f"[-] File {filename} not found.")
        return

    with open(filename, 'rb') as f:
        magic = f.read(4)
        if magic != b'cIMG':
            print("[-] Invalid Magic Number")
            return

        header_bytes = f.read(8)
        version, w, h, num_directives = struct.unpack("<HBBI", header_bytes)

        sprites = {}      
        stamped_order = [] 

        for _ in range(num_directives):
            op_chunk = f.read(2)

            if len(op_chunk) < 2: 
                break
            
            opcode = struct.unpack("<H", op_chunk)[0]

            if opcode == 3:
                params = f.read(3)
                sid, sw, sh = struct.unpack("BBB", params)
                sdata = f.read(sw * sh)
                sprites[sid] = {'w': sw, 'h': sh, 'data': sdata}

            elif opcode == 4:
                params = f.read(6)
                sid, r, g, b, x, y = struct.unpack("BBBBBB", params)
                stamped_order.append(sid)

        flag = ''
        
        for sid in stamped_order:
            blob = sprites[sid]['data']
            flag += lookup[blob]

        print(f'\n{flag}')

if __name__ == '__main__':
    parse_cimg('/challenge/flag.cimg')
```
