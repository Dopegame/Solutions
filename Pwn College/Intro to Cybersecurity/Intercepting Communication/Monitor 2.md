# Monitor 2

#### Using the wireshark GUI is too easy, so I went down a long rabbit hole to do it with "tshark". First need to collect packets, I didn't test the exact amount needed, but it was around 250 when I ended it:
```tshark -i 1 -f "port 31337" -w flag_i.pcap```

----------------------------------------------

#### That will save the packets to a file. Then it's piped into a python program to seperate the correct output. The first script printed all the alternating 1's if set to [1::2] and empty spaces when set to [::2] in lines. I had to debug the script to see why it wasnt printing:

```python
import sys

flag_chars = []
data = sys.stdin.read()

for line in data.splitlines():
    flag_char = line[::2]
    flag_chars.append(flag_char)

print('\n'.join(flag_chars))
```
```tshark -r flag_i.pcap -q -z follow,tcp,ascii,0 | python3 solutions.py```

#### Returned this

<img width="186" height="402" alt="Screenshot 2025-11-03 075126" src="https://github.com/user-attachments/assets/3e1ed5ee-4855-429e-82e0-2c1af5f6a28e" />


-------------------------------------------------------------

#### The debug

```python
import sys

data = sys.stdin.read()
for i, line in enumerate(data.splitlines()):
    print(f"Line {i+1} | Length: {len(line)} | Content: {repr (line)}")
    slice_even = line[::2]
    slice_odd = line[1::2]

    print(f" Slice [::2] (Odd indices) Length: {len(slice_odd)}")
    print(f" Slice [1::2] (Even indices) Length: {len(slice_even)}")
    print("-" * 20)

```

#### Returned that the chacaters for the flag were in odd spaces which didn't make sense

<img width="423" height="389" alt="Screenshot 2025-11-04 125130" src="https://github.com/user-attachments/assets/e80fd651-758a-40cf-8084-d93d68cc2dc3" />


------------------------------------------------------

#### Attempted fix:

```python
import sys

data = sys.stdin.read()
char_list = list(data)

filter_list = [char_list[i] for i in range(0, len(char_list), 2)]

result = data[1::2]
print(result)
```

#### Got closer but still not working:

<img width="953" height="89" alt="Screenshot 2025-11-04 132825" src="https://github.com/user-attachments/assets/478cec98-29c6-4d42-a0e9-618ac901e887" />


---------------------------------------------

#### After some research I realised I overcomplicated it and settled for an inline command piping the tshark into python:

```tshark -r flag_i.pcap -q -z follow,tcp,ascii,0 | python3 -c 'import sys; lines=sys.stdin.read().splitlines();print("".join(lines[1::2]))'```
