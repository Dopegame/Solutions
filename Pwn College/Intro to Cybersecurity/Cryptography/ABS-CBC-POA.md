# ABS-CBC-POA

#### This challenge took forever simply because I didn't check the source code. You have to pass 'pw' as an argument to /challenge/dispatcher for challenges 1 and 2, and then pass 'flag' on the third. Otherwise, you will decode to 'sleep'. I originally wrote a script to solve challenge 1, but realized it needed to handle multi blocks so I created this:
```python
from pwn import *
import string
from Crypto.Util.strxor import strxor
from Crypto.Cipher import AES
from Crypto.Util.Padding import unpad

context.log_level = 'error'
block_size = 16

# Get initial hex value and turn into bytes
pd = process(['/challenge/dispatcher', 'pw']) # Change the arg to 'flag' for multiblock challenge
pd.recvuntil(b"TASK: ")
base_hex = pd.recvline().decode('utf-8').strip()
print(f"Dispatch hex: {base_hex}")
base_bytes = bytes.fromhex(base_hex)

# Set IV and calculate ct blocks
ct_blocks = []
num_blocks = (len(base_bytes) - block_size) // block_size
for i in range(0, len(base_bytes), block_size):
    block = base_bytes[i:i + block_size]
    ct_blocks.append(block)

print(f"CT blocks: {ct_blocks}")

pw = process('/challenge/worker')

# Byte by byte check
def check_ct(iv: bytes, ct: bytes) -> bool:
    pak = (iv + ct).hex()
    pw.sendline(b"TASK: " + pak.encode())
    response = pw.recvline()
    if not b'command!' in response:
        return False
    else:
        return True

recovered = []
recovered_temp = b''
charset = bytes(range(256))
index = -1
inter_state = []
inter_state_temp = b''
blocks_checked = 0

while blocks_checked < len(ct_blocks):
    current_iv = ct_blocks[(len(ct_blocks) - 2) - blocks_checked]
    current_ct = ct_blocks[(len(ct_blocks) - 1) - blocks_checked]
        
    found_byte = False
        
    while True:

        current_recovered_len = len(recovered_temp)
        pad_value = current_recovered_len + 1

        pre_length = block_size - pad_value
        prefix = b'\x00' * pre_length
        target_pad_bytes = bytes([pad_value]) * current_recovered_len
        print(f"Attempting to XOR {inter_state_temp} and {target_pad_bytes}")
        suffix = strxor(inter_state_temp, target_pad_bytes)

        for ch in charset:
            modified_byte = bytes([ch ^ pad_value])
            guess_iv = prefix + modified_byte + suffix

            if check_ct(guess_iv, current_ct):
                xor_partner_index = block_size - pad_value
                original_iv_byte = bytes([current_iv[xor_partner_index]])
                pt_byte = strxor(bytes([ch]), original_iv_byte)
                    
                recovered_temp = pt_byte + recovered_temp
                inter_state_temp = bytes([ch]) + inter_state_temp       
                print(f"P[{xor_partner_index}]: {pt_byte.decode('latin1', errors='ignore')!r}")
                found_byte = True
                break

        if len(recovered_temp) == block_size:
            print(f"Block recovered: {recovered_temp}")
            blocks_checked += 1
            recovered.append(recovered_temp)
            inter_state.append(inter_state_temp)
            recovered_temp = b''
            inter_state_temp = b''
            print(f"Blocks checked: {blocks_checked} | Total blocks: {len(ct_blocks)}")
            break

    if blocks_checked == len(ct_blocks):
        print("All blocks decoded")
        break

# Arrange and decode password

recovered.reverse()

recovered_pt = recovered[1:]
password_bytes = unpad(b''.join(recovered_pt), block_size, style='pkcs7')

password = password_bytes.decode('latin1').strip()
print(f"Full plaintext: {password}")
pw.close()

# Send the password to get flag. This will error on multiblock challenge but the flag is still decoded.

pr = process('/challenge/redeem')
pr.sendline(password)
resp = pr.recvall()

slc = resp.split(b"pwn.college")[1].split(b"}")[0].decode('latin1').strip()
flag = (f"pwn.college{slc}" + '}')
print(flag)
pr.close()
```
