# Scan 1

#### The process is kinda slow but it was a quick way to get it done:
```python
import subprocess

prefix = "10.0.0."
start_h = 1
end_h = 254

ping_opt = ["-c", "1", "-w", "1"]

for host in range(start_h, end_h + 1):
    ip = prefix + str(host)
    command = ["ping"] + ping_opt + [ip]

    try:
        result = subprocess.run(command, capture_output=True, text=True, timeout=2)
        if result.returncode == 0:
            print(f"Good {ip}")
        else: 
            print(f"{ip} unreachable")
    except subprocess.TimeoutExpired:
        print(f"{ip} timed out")
    except Exception as e:
        print(f"error with {ip}")
```

-------------------------------------------------------

#### Then netcat whichever port comes back "good". *NOT 10.0.0.1*
```nc 10.0.0.89 31337```
