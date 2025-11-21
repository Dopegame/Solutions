# RSA 2

#### First have to calculate the public mod n. Then calculate Euler's Totient to get phi. Calculate the private key, then decrypt the ciphertext and reconstruct it.

#### Using pow:
```python
from Crypto.PublicKey import RSA
from pwn import *
import re

ch = process('/challenge/run')

resp = ch.recvall()

ct_h = re.search(rb"Flag Ciphertext\s*\(hex\)\s*:\s*([0-9a-fA-F]+)", resp).group(1)
p = int(re.search(r"p\s*=\s*(0x[0-9a-fA-F]+)", resp.decode()).group(1), 16)
q = int(re.search(r"q\s*=\s*(0x[0-9a-fA-F]+)", resp.decode()).group(1), 16)
e = int(re.search(r"e\s*=\s*(0x[0-9a-fA-F]+)", resp.decode()).group(1), 16)

n = (p * q)
phi = (p - 1) * (q - 1)
d = pow(e, -1, phi)

ct = bytes.fromhex(ct_h.decode())
m = pow(int.from_bytes(ct, "little"), d, n).to_bytes(256, "little")
flag = m.rstrip(b"\x00").rstrip(b"\n")
print("Flag:", flag.decode())
```

--------------------------------------------------------------------------

#### Can also write out the Euclidean algorithm to help understand the math behind it:
```python
from Crypto.PublicKey import RSA
from pwn import *
import re

ch = process('/challenge/run')

resp = ch.recvall()

ct_h = re.search(rb"Flag Ciphertext\s*\(hex\)\s*:\s*([0-9a-fA-F]+)", resp).group(1)
p = int(re.search(r"p\s*=\s*(0x[0-9a-fA-F]+)", resp.decode()).group(1), 16)
q = int(re.search(r"q\s*=\s*(0x[0-9a-fA-F]+)", resp.decode()).group(1), 16)
e = int(re.search(r"e\s*=\s*(0x[0-9a-fA-F]+)", resp.decode()).group(1), 16)

n = (p * q)
phi = (p - 1) * (q - 1)

def ex_gcd(a, b):
  if a == 0:
    return b, 0, 1
  else:
    gcd, x1, y1 = ex_gcd(b % a, a)
    x = y1 - (b // a) * x1
    y = x1
    return gcd, x, y

def mod_inv(e, phi):
  gcd, x, y = ex_gcd(e, phi)
  if gcd != 1:
    raise Exception('Does not exist, not coprime')
  else:
    return x % phi

d = mod_inv(e, phi)

ct = bytes.fromhex(ct_h.decode())
m = pow(int.from_bytes(ct, "little"), d, n).to_bytes(256, "little")
flag = m.rstrip(b"\x00").rstrip(b"\n")
print("Flag:", flag.decode())
