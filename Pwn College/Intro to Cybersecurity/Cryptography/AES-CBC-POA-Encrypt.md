# AES-CBC-POA-Encrypt

#### Similar to the decoding, just take the same logic and apply it the other way:
```python
#!/usr/bin/env python3
from pwn import *
from Crypto.Util.strxor import strxor
from Crypto.Util.Padding import pad
import sys
import os

context.log_level = 'error'
block_size = 16

# Choose message
msg_encypt_str = "please give me the flag, kind worker process!"
msg_to_encrypt = msg_encypt_str.encode('ascii')

if not msg_to_encrypt:
    print("Error: Message cannot be empty. Exiting.")
    sys.exit(1)

pw = process('/challenge/worker')

def check_ct(iv: bytes, ct: bytes) -> bool:
    pak = (iv + ct).hex()
    pw.sendline(b"TASK: " + pak.encode())
    response = pw.recvline().strip()

    if response == b'Unknown command!':
        return True
    else:    
        return False

def find_intermediate_state(ct_block: bytes) -> bytes:
    print(f"Finding Intermediate State (G) for block: {ct_block.hex()}")

    g_state = b''
    inter_state_temp = b''
    full_charset = bytes(range(256))
    
    for pad_value in range(1, block_size + 1):
        
        current_recovered_len = len(g_state)
        print(f"Targeting padding: {pad_value} ({hex(pad_value)})")
        
        pre_length = block_size - pad_value
        prefix = b'\x00' * pre_length
 
        target_pad_bytes = bytes([pad_value]) * current_recovered_len
        suffix = strxor(inter_state_temp, target_pad_bytes)
        found_g_byte = None
        
        for g_guess in full_charset:
            modified_byte = bytes([g_guess ^ pad_value])
            guess_iv = prefix + modified_byte + suffix
            
            if check_ct(guess_iv, ct_block):
                found_g_byte = g_guess
                break 
        
        if found_g_byte is None:
            print(f"Failed to find byte G[{block_size - pad_value}] for padding {pad_value}. Aborting block.")
            return b""
        
        g = found_g_byte
        
        g_state = bytes([g]) + g_state
        inter_state_temp = bytes([g]) + inter_state_temp 
        
        print(f"G[{block_size - pad_value}]: {hex(g)} (Pad: {pad_value})")
        
    print(f"Intermediate State (G) found: {g_state.hex()}")
    return g_state

# PKCS#7 Pad the Plaintext
padded_plaintext = pad(msg_to_encrypt, block_size)
log.info(f"Padded Plaintext: {padded_plaintext!r} (Length: {len(padded_plaintext)})")

# Split Plaintext into blocks
plaintext_blocks = []
for i in range(0, len(padded_plaintext), block_size):
    plaintext_blocks.append(padded_plaintext[i:i + block_size])

# Generate random Ciphertext blocks
forged_ciphertext = []

current_ct_block = os.urandom(block_size)
forged_ciphertext.append(current_ct_block)

print(f"Starting encryption with random C_N: {current_ct_block.hex()}")

for i in range(len(plaintext_blocks) - 1, -1, -1):
    
    current_p_block = plaintext_blocks[i]
    print(f"\n--- Forging Block {i+1} using Plaintext: {current_p_block!r} ---")
    
    G_i = find_intermediate_state(current_ct_block)

    if not G_i:
        print(f"Failed to find Intermediate State G for block {i}. Aborting.")
        pw.close()
        sys.exit(1)
        
    required_preceding_block = strxor(G_i, current_p_block)
    forged_ciphertext.append(required_preceding_block)
    current_ct_block = required_preceding_block 
    
    print(f"Calculated C_{i}: {required_preceding_block.hex()}")

forged_ciphertext.reverse()
final_payload_bytes = b''.join(forged_ciphertext)

print("Encryption complete.")
print(f"Original Message: {msg_encypt_str!r}")

pw.sendline(b"TASK: " + final_payload_bytes.hex().encode())
resp = pw.recvuntil(b'}')

slc = resp.split(b"pwn.college")[1].split(b"}")[0].decode('latin1').strip()
flag = (f"pwn.college{slc}" + '}')
print(flag)
pw.close()
```
