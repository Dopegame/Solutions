# Metadata and Data (Python)

#### First have to debug the program, start by looking at the surrounding code using ```python3 -m pdb /challenge/cimg```:
```python
-> import os
(Pdb) l
  1     #!/usr/bin/exec-suid -- /usr/bin/python3 -I
  2  
  3  -> import os
  4     import sys
  5     from collections import namedtuple
  6  
  7     Pixel = namedtuple("Pixel", ["ascii"])
  8  
  9  
 10     def main():
 11         if len(sys.argv) >= 2:
```
--------------------------------------------

#### Lets look inside the main() function by setting a break at line 11 continue and call l:
```python
(Pdb) b 11
Breakpoint 1 at /challenge/cimg:11
(Pdb) c
> /challenge/cimg(11)main()
-> if len(sys.argv) >= 2:
(Pdb) l
  6  
  7     Pixel = namedtuple("Pixel", ["ascii"])
  8  
  9  
 10     def main():
 11 B->     if len(sys.argv) >= 2:
 12             path = sys.argv[1]
 13             assert path.endswith(".cimg"), "ERROR: file has incorrect extension"
 14             file = open(path, "rb")
 15         else:
 16             file = sys.stdin.buffer
```
---------------------------------------------

#### Press enter a few times and the entire code is shown, giving all data needed:
```python
(Pdb) 
 17  
 18         header = file.read1(17)
 19         assert len(header) == 17, "ERROR: Failed to read header!"
 20  
 21         assert header[:4] == b"CN~R", "ERROR: Invalid magic number!"
 22  
 23 B       assert int.from_bytes(header[4:5], "little") == 1, "ERROR: Invalid version!"
 24  
 25         width = int.from_bytes(header[5:9], "little")
 26         assert width == 61, "ERROR: Incorrect width!"
 27  
(Pdb) 
 28         height = int.from_bytes(header[9:17], "little")
 29         assert height == 15, "ERROR: Incorrect height!"
 30  
 31         data = file.read1(width * height)
 32         assert len(data) == width * height, "ERROR: Failed to read data!"
 33  
 34         pixels = [Pixel(character) for character in data]
 35  
 36         with open("/flag", "r") as f:
 37             flag = f.read()
 38             print(flag)
```

-----------------------------------------------

#### The code has specific instructions. It takes the first byte of version, the full 4 bytes of width and height, then the length is width * height, so the code is:
```python
import struct

magic = b"CN~R"                
version = struct.pack("<B", 1)
width = struct.pack('<I', 61)
height = struct.pack('<Q', 15) 

data = 65 * 15
padding = b'\x00' * data

cimg = magic + version + width + height + padding

filename = "/home/hacker/s.cimg"
with open(filename, "wb") as f:
    f.write(cimg)
```

------------------------------------------------

#### Then run the script and use it as an argument as usual:
```python3 s.py; /challenge/cimg s.cimg```
