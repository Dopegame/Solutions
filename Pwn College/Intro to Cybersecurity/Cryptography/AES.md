# AES

#### Need to take the key and cipher, break it down to bytes and then run it through the decoder:

```python
from Crypto.Util.strxor import strxor
from Crypto.Util.Padding import unpad
from Crypto.Cipher import AES

aes_key = "c1a19d95f29cefc530aa82b2f5b518f8"
cipher_hex = "fd7179dcd58583723cfbce7502d6723aaba2183b8eca077ede29c6a232e8702d3e15a2990bb2251c4d817413ad76ada708441b8deadafb88b254e37681602cb4"

aes_dec = bytes.fromhex(aes_key)
cipher_dec = bytes.fromhex(cipher_hex)

cipher = AES.new(key=aes_dec, mode=AES.MODE_ECB)
flag = unpad(cipher.decrypt(cipher_dec), cipher.block_size)
print(flag.decode())
```
