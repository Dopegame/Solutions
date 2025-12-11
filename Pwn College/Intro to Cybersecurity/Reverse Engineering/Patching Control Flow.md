# Patching Control Flow

#### This and the previous two challenges were fairly simple. The actual issue was making it run on its own. I tried every way I could think to code the bot to move. Complex pathfinding, complex bomb avoidance, forced lock step and more. The issue stems from the fact that it's being piped from a python script. The final script was almost perfect, but because of the buffer issues, the bot would always move 1-3 pixels to far and hit a bomb. The only way I found to stop this was slow down the refresh so much that I may as well play the game myself, and so I did.
```python
from pwn import *
import sys
import struct
import signal
import termios
import tty
import threading

context.log_level = 'error'

def handle_visuals(quest, cimg):
    width = 0
    height = 0
    
    try:
        header = quest.recvn(12)
        cimg.send(b'cIMG' + header[4:])
        width = u8(header[6:7])
        height = u8(header[7:8])
    except (EOFError, KeyboardInterrupt):
        return

    while True:
        try:
            code = quest.recvn(2)
        except (EOFError, Exception):
            break

        cimg_1337 = u16(code)
        cimg_normal = 0
        patch = b''
        visuals = True

        if cimg_1337 == 7:
            cimg_normal = 1
            patch = quest.recvn(width * height * 4)

        elif cimg_1337 == 6:
            cimg_normal = 2
            hdr = quest.recvn(4)
            patch += hdr
            w_ = u8(hdr[2:3])
            h_ = u8(hdr[3:4])
            data = quest.recvn(w_ * h_ * 4)
            patch += data

        elif cimg_1337 == 5:
            cimg_normal = 3
            hdr = quest.recvn(3)
            patch += hdr
            patch += quest.recvn(u8(hdr[1:2]) * u8(hdr[2:3]))

        elif cimg_1337 == 4:
            cimg_normal = 4
            patch = quest.recvn(9)

        elif cimg_1337 == 2:
            cimg_normal = 6
            patch = quest.recvn(1)

        elif cimg_1337 == 1:
            cimg_normal = 7
            patch = quest.recvn(4)

        else: continue

        if visuals:
            try:
                cimg.send(p16(cimg_normal) + patch)
            except BrokenPipeError:
                break

def game():
    quest = process(['/challenge/quest.py'], stdin=PTY, stdout=PIPE)
    cimg = process(['/challenge/cimg'], stdin=PIPE, stdout=sys.stdout)

    t = threading.Thread(target=handle_visuals, args=(quest, cimg))
    t.daemon = True
    t.start()
    fd = sys.stdin.fileno()
    old_settings = termios.tcgetattr(fd)

    try:
        tty.setcbreak(sys.stdin.fileno())
        
        while True:
            ch = sys.stdin.read(1)            
            if not ch or ch == 'q':
                break
            if ch == '\x03': 
                break
            quest.send(ch.encode())

    except Exception as e:
        pass
        
    finally:
        termios.tcsetattr(fd, termios.TCSADRAIN, old_settings)
        quest.close()
        cimg.close()
        print("\nGame Closed.")

if __name__ == '__main__':
    game()
```
