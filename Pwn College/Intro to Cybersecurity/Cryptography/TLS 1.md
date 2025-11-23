# TLS 1

#### First need to collect the data. Decode the certificate to get e and n. Have to create keys and then create the json package to make a certificate and signature. When the correct combo is sent the ciphertext is given, have to mod n and decode:
```python
from pwn import *
import base64
import re
from Crypto.PublicKey import RSA
import hashlib
import json

context.log_level = 'error'
io = process('/challenge/run')

info = io.recvuntil(b"user certificate (b64):")
d = int(re.search(rb"root key d:\s*0x([0-9a-zA-Z]+)", info).group(1), 16)
rc = re.search(rb"root certificate \(b64\):\s*([0-9a-zA-Z+/]+={0,2})", info).group(1)
rcs = re.search(rb"root certificate signature \(b64\):\s*([0-9a-zA-Z+/]+={0,2})", info).group(1)

pkr = base64.b64decode(rc)

e = int(re.search(rb'"e":\s*(\d+)', pkr).group(1))
n = int(re.search(rb'"n":\s*(\d+)\s*\}', pkr).group(1))

key = RSA.generate(1024, e=65537)
u_n = key.n
u_e = key.e
u_d = key.d

u_c_json = {"name": "user", "key": {"e": u_e, "n": u_n}, "signer": "root"}

u_c_data = json.dumps(u_c_json, separators=(',', ':')).encode('utf-8')

u_cert = base64.b64encode(u_c_data).decode('utf-8')
byte_len = (n.bit_length() + 7) // 8
c_hash_bytes = hashlib.sha256(u_c_data).digest()
h_int = int.from_bytes(c_hash_bytes, 'little')

s_int = pow(h_int, d, n)

s_bytes = s_int.to_bytes(byte_len, 'little')
u_sig = base64.b64encode(s_bytes).decode('utf-8')

io.sendline(u_cert.encode())
io.sendline(u_sig.encode())
ct = io.recvall()
c_b64 = re.search(rb'secret ciphertext \(b64\):\s*([a-zA-Z0-9+/]+={0,2})', ct).group(1).strip()
c = base64.b64decode(c_b64)

c_d = pow(int.from_bytes(c, 'little'), u_d, u_n)
f_b = c_d.to_bytes(256, 'little')
flag = f_b.decode('latin1').strip('\0xx')
print(flag)
