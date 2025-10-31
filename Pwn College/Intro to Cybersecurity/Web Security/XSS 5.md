# XSS 5

#### This one didn't make sense at first till I really looked into what it required. Admin has a draft of the flag that needs to be force published. Based on that, have to craft a <script> to run with /challenge/victim to make this happen.
#### Have to do this challenge in the desktop. Head to http://challenge.localhost. Log in as hacker - 1337. In the text box enter:

```
<script>fetch("challenge.localhost/publish", {method: 'GET'})</script>
```
-----------------------------------------------------------
#### Click the publish box and save. Then another post with:

```<script>alert("PWNED")</script>```

-----------------------------------------------------------
#### Run /challenge/victim and refresh the page.
