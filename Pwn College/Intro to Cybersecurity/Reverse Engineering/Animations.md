# Animations

#### Another picture flag:
```python
import struct
import sys
from pwn import *

def parse_cimg(filename):
    print(f"[*] Parsing {filename}...")
    
    with open(filename, "rb") as f:
        magic = f.read(4)
        
        if magic != b'cIMG':
            print("[-] Invalid Magic Header")
            return

        header_data = f.read(8)
        version, width, height, num_directives = struct.unpack("<HBBI", header_data)
        
        print(f"[*] Canvas: {width}x{height}")
        print(f"[*] Processing {num_directives} directives...")

        grid = [[' ' for _ in range(width)] for _ in range(height)]

        for _ in range(num_directives):
            chunk = f.read(2)

            if not chunk: break
            opcode = struct.unpack("<H", chunk)[0]

            if opcode == 2:
                data = f.read(8)
                
                x = data[0]
                y = data[1]
                char_code = data[7]
                
                if y < height and x < width:
                    grid[y][x] = chr(char_code)

            elif opcode == 6:
                f.read(1)

            elif opcode == 7:
                f.read(4)

            else:
                print(f"[!] Unknown opcode {opcode} at file offset {f.tell()}")
                break

    print("\n" + "="*width)
    for row in grid:
        print("".join(row))
    print("="*width + "\n")

if __name__ == "__main__":
    io = process('/challenge/generate_flag_image', shell=True)
    io.close()
    parse_cimg("/challenge/flag.cimg")
```
