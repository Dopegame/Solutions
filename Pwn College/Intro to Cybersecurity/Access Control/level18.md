# level18

#### Same script will work again:
```python
# --- TSA.py (Fixed) ---
from pwn import *
import re

context.log_level = 'error'
io = process('/challenge/run')

level = {
    "TS": 4,
    "S": 3,
    "C": 2,
    "UC": 1
}

io.recvuntil(b'Q')

while True:
    try:
        q = (b'Q' + io.recvuntil(b'?', timeout=0.1)).decode().strip()
    except:
        break

    data = re.search(r'Q\s*\d+\.\s*Can a Subject with level\s*(?P<SL>\w+)\s*and categories\s*{(?P<SC>.*?)}\s*(?P<Action>read|write) an Object with level\s*(?P<OL>\w+)\s*and categories\s*{(?P<OC>.*?)}', q)

    sub_lvl = data.group('SL')
    obj_lvl = data.group('OL')
    action = data.group('Action')

    sub_cat = set(re.findall(r'\w+', data.group('SC')))
    obj_cat = set(re.findall(r'\w+', data.group('OC')))

    if action == "write":
        if level[sub_lvl] <= level[obj_lvl] and sub_cat.issubset(obj_cat):
            io.sendline(b"yes")
        else:
            io.sendline(b"no")
    elif action == "read":
        if level[sub_lvl] >= level[obj_lvl] and sub_cat.issuperset(obj_cat):
            io.sendline(b"yes")
        else:
            io.sendline(b"no")

fb = io.recvall().decode()
flag = re.search(r'(pwn\.college\{.*?\})', fb, re.DOTALL).group(1)
print(flag)
```
