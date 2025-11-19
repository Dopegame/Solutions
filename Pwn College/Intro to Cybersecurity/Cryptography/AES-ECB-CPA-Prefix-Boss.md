# AES-ECB-CPA-Prefix-Boss

#### Just need to alter the script for requests:
```python
import requests
import base64
import string

url = "http://challenge.localhost"
charset = string.printable.strip().encode()
block_size = 16
ref_block = 1
target_block = 2
pad = 0
flag_len = 64

session = requests.Session()

def reset():
    session.post(f"{url}/reset")

def encrypt_prefix(prefix: bytes) -> bytes:
    session.post(url, data={'content': prefix.decode('latin1')})
    resp = session.get(url).text
    ct_b64 = resp.split("<pre>")[1].split("</pre>")[0].strip()
    return base64.b64decode(ct_b64).hex()

def get_block(ct: bytes, n: int) -> bytes:
    start = (n - 1) * block_size * 2
    end = start + block_size * 2
    return ct[start:end]

recovered = b""

while len(recovered) < flag_len:
    pkcs_byte = max(15 - len(recovered), 0)
    padding = bytes([pkcs_byte]) * pad
    found = False

    for ch in charset:
        guess = bytes([ch]) + recovered + padding
        reset()
        ct = encrypt_prefix(guess)

        block_ref = get_block(ct, ref_block)
        block_target = get_block(ct, target_block)

        if block_ref == block_target:
            recovered = bytes([ch]) + recovered
            found = True
            break

    if not found:
        print(f"Failed to find byte with {pad} padding in target block: {target_block}")
        pad += 1
        if pad == 30:
            print(f"Moving target block to {target_block + 1}")
            target_block += 1
            pad = 0

    if recovered.startswith(b"pwn") and recovered.endswith(b"}"):
        print(f"\n[*] Final flag: {recovered.decode(errors='replace')}")
        break
```
