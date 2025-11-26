# A Basic cIMG (Python)

#### Run the ```python3 -m pdb /challenge/cimg``` command, set a break at 11, continue to the break and enter for the script:
```python
hacker@reverse-engineering~a-basic-cimg-python:~$ python3 -m pdb /challenge/cimg
> /challenge/cimg(3)<module>()
-> import os
(Pdb) l
  1     #!/usr/bin/exec-suid -- /usr/bin/python3 -I
  2  
  3  -> import os
  4     import sys
  5     from collections import namedtuple
  6  
  7     Pixel = namedtuple("Pixel", ["r", "g", "b", "ascii"])
  8  
  9  
 10     def main():
 11         if len(sys.argv) >= 2:
(Pdb) b 11
Breakpoint 1 at /challenge/cimg:11
(Pdb) l
 12             path = sys.argv[1]
 13             assert path.endswith(".cimg"), "ERROR: file has incorrect extension"
 14             file = open(path, "rb")
 15         else:
 16             file = sys.stdin.buffer
 17  
 18         header = file.read1(13)
 19         assert len(header) == 13, "ERROR: Failed to read header!"
 20  
 21         assert header[:4] == b"cIMG", "ERROR: Invalid magic number!"
 22  
(Pdb) 
 23         assert int.from_bytes(header[4:8], "little") == 2, "ERROR: Invalid version!"
 24  
 25         width = int.from_bytes(header[8:12], "little")
 26         assert width == 48, "ERROR: Incorrect width!"
 27  
 28         height = int.from_bytes(header[12:13], "little")
 29         assert height == 22, "ERROR: Incorrect height!"
 30  
 31         data = file.read1(width * height * 4)
 32         assert len(data) == width * height * 4, "ERROR: Failed to read data!"
 33  
(Pdb) 
 34         pixels = [Pixel(*data[i : i + 4]) for i in range(0, len(data), 4)]
 35  
 36         invalid_character = next((pixel.ascii for pixel in pixels if not (0x20 <= pixel.ascii <= 0x7E)), None)
 37         assert invalid_character is None, f"ERROR: Invalid character {invalid_character:#04x} in data!"
 38  
 39         ansii_escape = lambda pixel: f"\x1b[38;2;{pixel.r:03};{pixel.g:03};{pixel.b:03}m{chr(pixel.ascii)}\x1b[0m"
 40         framebuffer = "".join(
 41             "".join(ansii_escape(pixel) for pixel in pixels[row_start : row_start + width])
 42             + ansii_escape(Pixel(0, 0, 0, ord("\n")))
 43             for row_start in range(0, len(pixels), width)
 44         )
(Pdb) 
 45         print(framebuffer)
 46  
 47         nonspace_count = sum(1 for pixel in pixels if chr(pixel.ascii) != " ")
 48         if nonspace_count != 1056:
 49             return
 50  
 51         asu_maroon = (0x8C, 0x1D, 0x40)
 52         if any((pixel.r, pixel.g, pixel.b) != asu_maroon for pixel in pixels):
 53             return
 54  
 55         with open("/flag", "r") as f:
(Pdb) 
 56             flag = f.read()
 57             print(flag)
 58  
 59  
 60     if __name__ == "__main__":
 61         try:
 62             main()
 63         except AssertionError as e:
 64             print(e, file=sys.stderr)
 65             sys.exit(-1)
[EOF]
(Pdb)
```
------------------------------------------

#### Here's all the data and the requirement for the total length as well as the color needed:
```python
23         assert int.from_bytes(header[4:8], "little") == 2, "ERROR: Invalid version!"
 24  
 25         width = int.from_bytes(header[8:12], "little")
 26         assert width == 48, "ERROR: Incorrect width!"
 27  
 28         height = int.from_bytes(header[12:13], "little")
 29         assert height == 22, "ERROR: Incorrect height!"
 30  
 31         data = file.read1(width * height * 4)
 32         assert len(data) == width * height * 4, "ERROR: Failed to read data!"

47         nonspace_count = sum(1 for pixel in pixels if chr(pixel.ascii) != " ")
 48         if nonspace_count != 1056:
 49             return
 50  
 51         asu_maroon = (0x8C, 0x1D, 0x40)
 52         if any((pixel.r, pixel.g, pixel.b) != asu_maroon for pixel in pixels):
 53             return
```
-----------------------------------------------

#### Same as the other challenges but now adding color. Just have to set the script to color each "pixel":
```python
import struct

w = 48
h = 22


magic = b"cIMG"                
version = struct.pack('<I', 2)
width = struct.pack('<I', w)
height = struct.pack('<B', h) 

total = w * h

r = b'\x8c'
g = b'\x1d'
b = b'\x40'
char = b'S'
pixel = r + g + b + char
data = pixel * total

cimg_base = magic + version + width + height

cimg = cimg_base + data

filename = "/home/hacker/s.cimg"
with open(filename, "wb") as f:
    f.write(cimg)
```
