# RSA Signatures

#### First have to cat the key files to get e and n. Then turn "flag" and a random message "m_r" into ints using 'little' endian. Next calculate the inverse of m_r and m_flag modulo n. Convert both back into bytes and then into b64 and submit each to the dispatcher as an argument. b64 decode both, convert both into ints and then multiply them to modulo n. Convert to bytes again with 256 and 'little' endian, b64 encode and send to the worker as an argument:
```python
from Crypto.PublicKey import RSA
from pwn import *
import math
import base64
import re

context.log_level = 'error'

n = 0xe31e11966885ad2bdbc7b13f6d3ae8ec767ae7488e34145a3261ca5c7cb5111843a4b2b1201eca0ef8bbe75372ac4adf15e17076b1d9d0386c41fbd4ddf516699b82acec6be20f332fe966e2182f42747d54239263aebd2471154f278f2dcef9f2e7aaaa8d781c237f1be037f61f232b93a348fe218e5a610645e4c9c398ba4dd2a3872af38a4e35d0d73e3b53a62ba4d4b5317cf11323cf7674550c89ab4430dfb35506380e5eafc21924280403c9cb908e251639a0cfdb541f44c70c0c38f3a92835a2e35be1938d486630a18c43e1d9a2e967edf956753696856d2243b3da162e48d79ceb17d07aa2f240612912a3f8ec68442e10d71246dc6ac30f5c00ad
e = 0x10001

m_flag = int.from_bytes(b'flag', 'little')
m_test = int.from_bytes(b'test', 'little')

m_r_inverse = pow(m_test, -1, n)
m_a = (m_flag * m_r_inverse) % n

s_r_bytes = m_test.to_bytes(256, 'little')
s_a_bytes = m_a.to_bytes(256, 'little')

s_r_b64 = base64.b64encode(s_r_bytes)
s_a_b64 = base64.b64encode(s_a_bytes)

def dispatch(arg):
    cd = process(['/challenge/dispatcher', arg])
    cd.recvuntil(b"Signed command (b64): ")
    resp = cd.recvline().strip()
    return resp

s_r_b = dispatch(s_r_b64)
s_a_b = dispatch(s_a_b64)

s_r = base64.b64decode(s_r_b)
s_a = base64.b64decode(s_a_b)

s_r_i = int.from_bytes(s_r, 'little')
s_a_i = int.from_bytes(s_a, 'little')

f_i = (s_r_i * s_a_i) % n

f_b = f_i.to_bytes(256, 'little')
f = base64.b64encode(f_b)

cw = process(['/challenge/worker', f])
msg = cw.recvall()
search = rb"(pwn\.college\{.*?\})"
match = re.search(search, msg)
flag = match.group(1) 
print(flag.decode())
```
