# TLS 2

#### Pretty much the same as the last one except now there's an additional signature and the flag has to be decrypted.
```python
from pwn import *
import base64
import re
from Crypto.PublicKey import RSA
import hashlib
from Crypto.Hash.SHA256 import SHA256Hash
import json
from Crypto.Util.Padding import pad, unpad
from Crypto.Cipher import AES
from Crypto.Random.random import getrandbits

context.log_level = 'error'
io = process('/challenge/run')

# Receive data
info = io.recvuntil(b"B:")
p = int(re.search(rb"p:\s*0x([0-9a-zA-Z]+)", info).group(1), 16)
g = int(re.search(rb"g:\s*0x([0-9a-zA-Z]+)", info).group(1))
d = int(re.search(rb"root key d:\s*0x([0-9a-zA-Z]+)", info).group(1), 16)
rc = re.search(rb"root certificate \(b64\):\s*([0-9a-zA-Z+/]+={0,2})", info).group(1)
rcs = re.search(rb"root certificate signature \(b64\):\s*([0-9a-zA-Z+/]+={0,2})", info).group(1)
name = re.search(rb"name:\s*([0-9a-zA-Z]+)", info).group(1).decode().strip()
A = int(re.search(rb"A:\s*0x([0-9a-zA-Z]+)", info).group(1), 16)

# Generate B and s
b = getrandbits(2048)
B = pow(g, b, p)
s = pow(A, b, p)

# Create DH key
aes_key = SHA256Hash(s.to_bytes(256, 'little')).digest()[:16]
c_encrypt = AES.new(key=aes_key, mode=AES.MODE_CBC, iv=b"\0"*16)
c_decrypt = AES.new(key=aes_key, mode=AES.MODE_CBC, iv=b"\0"*16)

# Decode and get e and n
pkr = base64.b64decode(rc)
e = int(re.search(rb'"e":\s*(\d+)', pkr).group(1))
n = int(re.search(rb'"n":\s*(\d+)\s*\}', pkr).group(1))

# Generate RSA keys
rsa_key = RSA.generate(1024, e=65537)
u_n = rsa_key.n
u_e = rsa_key.e
u_d = rsa_key.d

# Setup json and cert data
u_c_json = {"name": name, "key": {"e": u_e, "n": u_n}, "signer": "root"}
u_c_data = json.dumps(u_c_json, separators=(',', ':'), sort_keys=True).encode()
pad_u_c_data = pad(u_c_data, 16)
encrypted_c_bytes = c_encrypt.encrypt(pad_u_c_data)

u_cert = base64.b64encode(encrypted_c_bytes).decode('utf-8')

byte_len = (n.bit_length() + 7) // 8
c_hash_bytes = SHA256Hash(u_c_data).digest()
h_int = int.from_bytes(c_hash_bytes, 'little')

s_int = pow(h_int, d, n)

s_bytes = s_int.to_bytes(256, 'little')
pad_s_bytes = pad(s_bytes, 16)
encrypted_s_bytes = c_encrypt.encrypt(pad_s_bytes)
u_sig = base64.b64encode(encrypted_s_bytes).decode('utf-8')


user_sig_data = (
    name.encode().ljust(256, b"\0") +
    A.to_bytes(256, "little") +
    B.to_bytes(256, "little")
)

user_sig_hash = SHA256Hash(user_sig_data).digest()
user_sig_h_int = int.from_bytes(user_sig_hash, 'little')

user_sig_s_int = pow(user_sig_h_int, u_d, u_n)
user_sig_s_bytes = user_sig_s_int.to_bytes(256, 'little')

padded_user_sig = pad(user_sig_s_bytes, 16)
encrypted_user_sig = c_encrypt.encrypt(padded_user_sig)
u_sig_final = base64.b64encode(encrypted_user_sig).decode('utf-8')

# Send data
io.sendline(hex(B).encode())
io.sendline(u_cert.encode())
io.sendline(u_sig.encode())
io.sendline(u_sig_final.encode())

# Receive and decode flag
ct = io.recvall()
c_b64 = re.search(rb'secret ciphertext \(b64\):\s*([a-zA-Z0-9+/]+={0,2})', ct).group(1).strip()
c = base64.b64decode(c_b64)
d_f_b = c_decrypt.decrypt(c)
f_b = unpad(d_f_b, AES.block_size)
flag = f_b.decode('latin1').strip('\x00')
print(flag)

```
