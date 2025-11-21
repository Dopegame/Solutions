# RSA 4

#### Have to generate keys and submit e and n to get a challenge. Use the challenge to "sign" and find s. After receiving the ciphertext, same as previous challenges decoding back:
```python
from Crypto.PublicKey import RSA
from pwn import *
import math
import base64

e = 65537

while True:
    key = RSA.generate(1024, e=e)
    n = key.n
    p = key.p
    q = key.q
    d = key.d

    phi = (p - 1) * (q - 1)

    if math.gcd(e, phi) == 1:
        break
    else:
        pass

ch = process('/challenge/run')

ch.recvuntil(b"e:")
ch.sendline(f"{e:#x}".encode())

ch.recvuntil(b"n:")
ch.sendline(f"{n:#x}".encode())

ch.recvuntil(b"challenge: ")
challenge = ch.recvline().strip().decode()

ch.recvuntil(b"response:")
s = pow(int(challenge, 16), int(d), int(n))
ch.sendline(f"{s:#x}".encode())

rs = ch.recvall()
b64_ct = rs.split(b"secret ciphertext (b64):")[1].strip()

ct_bytes = base64.b64decode(b64_ct)
ct_int = int.from_bytes(ct_bytes, 'little')
flag_int = pow(ct_int, int(d), int(n))
flag = flag_int.to_bytes(256, 'little')

print(flag.decode('latin1').strip('\x00'))
```
